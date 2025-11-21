# How It Works

This section explains the core workflows and business logic of the DonarApp backend through code examples from the most important services.

## Authentication Flow

The authentication system handles user registration, login, email verification, and password recovery.

### User Registration

When a user registers, the system:
1. Validates and hashes the password
2. Creates the user record
3. Sends a verification email with a code and token
4. Logs the action in the audit system

```typescript
async registerUser(dto: CreateUserDto): Promise<{ message: string, statussCode: number }> {
  // Validate password
  if(!dto.password || dto.password.length < 8) {
    throw new InternalServerErrorException('La contraseña debe tener al menos 8 caracteres.');
  }
  
  // Hash password
  const salt = await bcrypt.genSalt(10);
  const hashedPassword = await bcrypt.hash(dto.password, salt);
  dto.password = hashedPassword;
  
  // Create user
  user = await this.userService.create(dto);
  
  // Send verification email
  await this.sendEmailVerification(user.email, user.username, user.id);
  
  // Add user to system terms
  await this.userSystemService.addUserToSystem(user.id);
  
  // Log action
  await this.auditService.createLog(user.id, 'registerUser', ...);
  
  return { message: 'Usuario registrado exitosamente.', statussCode: 200 };
}
```

### Login with Security

The login process includes:
- Account lock mechanism after failed attempts
- Email verification check
- Block status validation
- JWT token generation

```typescript
async login(dto: LoginDto): Promise<any> {
  const { email, password } = dto;
  const user = await this.userRepository.findOne({ where: { email }, relations: ['rol'] });
  
  if (!user) {
    throw new UnauthorizedException('Credenciales inválidas.');
  }
  
  // Check email verification
  if (!user.emailVerified) {
    throw new UnauthorizedException('Correo electrónico no verificado.');
  }
  
  // Check if account is blocked
  if (user.block) {
    throw new UnauthorizedException('Cuenta de usuario bloqueada.');
  }
  
  // Check temporary lock (after multiple failed attempts)
  if (this.isLocked(user)) {
    const minutes = Math.ceil((user.lockUntil!.getTime() - Date.now()) / 60000);
    throw new UnauthorizedException(`Cuenta bloqueada temporalmente. Intenta en ${minutes} min.`);
  }
  
  // Verify password
  const isPasswordMatching = await bcrypt.compare(password, user.password);
  if (!isPasswordMatching) {
    await this.registerFailedAttempt(user);
    throw new UnauthorizedException('Credenciales inválidas.');
  }
  
  // Reset login attempts and update last login
  await this.resetLoginAttempts(user.id);
  await this.updateLastLogin(user.id);
  
  // Generate JWT token
  const token = await this.generateToken(user);
  
  return {
    message: 'Inicio de sesión exitoso.',
    access_token: token.access_token,
  };
}
```

---

## Donation Creation Workflow

The donation system is the core of the application. It handles complex validation logic to ensure donations are valid and properly tracked.

### Creating a Donation

The donation creation process:
1. Validates the user is verified
2. Checks the post exists and validates article availability
3. Determines donor/beneficiary based on post type
4. Creates the donation record
5. Links articles to the donation
6. Sends notifications to the post owner

```typescript
async createDonation(createDonationDto: CreateDonationDto, currentUser: any): Promise<DonationEntity> {
  const { postId, lugarRecogida, articles, fechaMaximaEntrega } = createDonationDto;
  
  // Validate user is verified
  const currentUserEntity = await this.userService.findById(currentUserId);
  if (!currentUserEntity.verified) {
    throw new ForbiddenException('Solo usuarios verificados pueden crear donaciones');
  }
  
  // Get post and validate
  const post = await this.postService.getPostById(Number(postId));
  if (!post) throw new NotFoundException('Post no encontrado');
  
  // Determine if this is a "solicitud de donacion" (request for donation)
  const typePostName = post.typePost?.type?.toLowerCase?.() || null;
  const isSolicitudDonacion = typePostName === 'solicitud de donacion';
  
  // Determine receiver based on post type
  let receiver: any;
  if (isSolicitudDonacion) {
    // For donation requests, the post owner receives
    receiver = post.user;
    if (currentUserEntity.id === post.user.id) {
      throw new ForbiddenException('No puedes donar a tu propia solicitud');
    }
  } else {
    // For donation offers, the current user receives
    receiver = currentUserEntity;
    if (currentUserEntity.id === post.user.id) {
      throw new ForbiddenException('No puedes crear una donación para tu propio post');
    }
  }
  
  // Validate receiver is an organization
  const receiverRol = receiver.rol?.rol?.toLowerCase();
  if (receiverRol !== 'organizacion') {
    throw new ForbiddenException('Solo usuarios con rol "organizacion" pueden recibir donaciones');
  }
  
  // Validate articles availability
  const postArticles = await this.postArticleService.findByPost(Number(postId));
  const statusAvailable = await this.statusArticleDonationService.getStatusByName('disponible');
  
  for (const item of articles) {
    const articleData = postArticleMap.get(item.articlePostId);
    
    // Check quantity
    if (item.quantity > articleData.quantity) {
      throw new BadRequestException('Cantidad solicitada excede la disponible');
    }
    
    // Check status
    if (articleData.statusId !== statusAvailable.id) {
      throw new BadRequestException('Artículo no disponible para donación');
    }
  }
  
  // Create donation
  const donation = this.donationRepo.create({
    lugarRecogida,
    fechaMaximaEntrega,
    statusDonation: statusPendiente,
    user: receiver,
    post: { id: post.id },
  });
  
  const savedDonation = await this.donationRepo.save(donation);
  
  // Link articles to donation
  for (const [postArticleId, quantity] of requestedByPostArticle.entries()) {
    await this.postDonationArticleService.addArticleToDonation({
      postArticleId,
      donationId: savedDonation.id,
      quantity
    });
  }
  
  // Send notification to post owner
  await this.notifyService.createNotify({
    title: 'Nueva Donación',
    message: `Nueva donación creada para tu post "${post.title}"`,
    link: `${urlFront}/organization/donations/${savedDonation.id}`,
    typeNotifyId: typeNotifyId,
    usersIds: [postOwnerUserId]
  });
  
  return donationWithArticles;
}
```

---

## Post Management

Posts are the central entity for donation offers and requests. The system handles image uploads, tagging, and article management.

### Creating a Post with Images and Articles

```typescript
async createPost(data: any, files?: Express.Multer.File[]): Promise<PostEntity> {
  const { tags, typePost, user, articles, ...rest } = data;
  
  // Validate required fields
  if (!rest.title || !rest.message) {
    throw new Error('title y message son obligatorios');
  }
  
  // Validate post type
  const existTypePost = await this.typePostService.findById(typePost.id);
  if (!existTypePost) {
    throw new NotFoundException('El tipo de post no existe');
  }
  
  // Special validation for "solicitud de donacion"
  if (existTypePost.type.toLowerCase() === 'solicitud de donacion') {
    const userEntity = await this.userService.findById(userId);
    const userRol = userEntity.rol?.rol?.toLowerCase();
    
    if (userRol !== 'organizacion') {
      throw new ForbiddenException(
        'Solo usuarios con rol "organizacion" pueden crear solicitudes de donación'
      );
    }
  }
  
  // Validate file sizes
  if (files && files.length > 0) {
    for (const file of files) {
      const validation = await this.imagePostService.verifyFileSize(file);
      if (validation.status === 413) {
        throw new HttpException('Archivo demasiado grande', 413);
      }
    }
  }
  
  // Create post
  const newPost = this.postRepository.create({
    title: rest.title,
    message: rest.message,
    user: { id: userId },
    typePost: existTypePost,
  });
  
  const savedPost = await this.postRepository.save(newPost);
  const postId = savedPost.id;
  
  // Add tags
  if (tags) {
    for (const tagName of tags) {
      let tagEntity;
      try {
        tagEntity = await this.tagsService.getTabByName(tagName);
      } catch (err) {
        tagEntity = await this.tagsService.createTag(tagName);
      }
      await this.postTagsService.createPostTag(postId, tagEntity.id);
    }
  }
  
  // Add articles
  if (articles && articles.length > 0) {
    for (const item of articles) {
      let articleId;
      
      // Check if article exists by name
      try {
        const exist = await this.articleService.getArticleByName(item.name);
        articleId = exist?.id;
      } catch (err) {
        // Create new article if it doesn't exist
        const created = await this.articleService.createArticle({
          name: item.name,
          description: item.description
        });
        articleId = created?.id;
      }
      
      // Link article to post
      await this.postArticleService.addPostArticle({
        post: postId,
        article: articleId,
        quantity: item.quantity || 1
      }, userId);
    }
  }
  
  // Upload images
  if (files && files.length > 0) {
    for (const file of files) {
      await this.imagePostService.addImageToPost(postId, file);
    }
  }
  
  return await this.getPostById(postId);
}
```

### Optimized Post Retrieval with Like Status

The system efficiently retrieves posts with user-specific data (like status) without N+1 queries:

```typescript
async findAll(userId?: number, limit: number = 20, cursor?: number): Promise<PostEntity[]> {
  // Build query with all relations
  const query = this.postRepository.createQueryBuilder('post')
    .leftJoinAndSelect('post.imagePost', 'imagePost')
    .leftJoinAndSelect('post.tags', 'postTags')
    .leftJoinAndSelect('postTags.tag', 'tag')
    .leftJoinAndSelect('post.user', 'user')
    .leftJoinAndSelect('post.postArticle', 'postArticle')
    .leftJoinAndSelect('postArticle.article', 'article')
    .leftJoinAndSelect('post.typePost', 'typePost');
  
  // Cursor-based pagination
  if (cursor && cursor > 0) {
    query.andWhere('post.id < :cursor', { cursor });
  }
  
  query.orderBy('post.id', 'DESC').take(limit);
  
  const posts = await query.getMany();
  
  // Batch query to check which posts the user has liked (avoid N+1)
  let userLikedPostIds: Set<number> = new Set();
  if (userId && userId > 0) {
    const postIds = posts.map(post => post.id);
    const userLikes = await this.postRepository
      .createQueryBuilder('post')
      .innerJoin('post.postLiked', 'liked')
      .where('post.id IN (:...postIds)', { postIds })
      .andWhere('liked.user.id = :userId', { userId })
      .select('post.id')
      .getMany();
    
    userLikedPostIds = new Set(userLikes.map(post => post.id));
  }
  
  // Enrich posts with user-specific data
  return posts.map(post => {
    // Sanitize user data
    if (post.user) {
      const { id, username, profilePhoto, emailVerified, verified } = post.user;
      post.user = { id, username, profilePhoto, emailVerified, verified };
    }
    
    // Add like status
    if (userId && userId > 0) {
      (post as any).userHasLiked = userLikedPostIds.has(post.id);
    }
    
    // Add likes count
    (post as any).likesCount = post.postLiked?.length || 0;
    
    return post;
  });
}
```

---

## Key Design Patterns

### 1. **Audit Logging**
Every critical operation is logged with user ID, action, payload, and response for security and debugging.

### 2. **Role-Based Access Control**
The system enforces strict role checks, especially for organization-only features like receiving donations.

### 3. **Optimized Queries**
Uses batch queries and `loadRelationCountAndMap` to avoid N+1 query problems.

### 4. **Data Sanitization**
Sensitive user data (passwords, tokens, etc.) is always removed before sending responses.

### 5. **Notification System**
Automatically notifies users of important events (new donations, status changes, etc.).
