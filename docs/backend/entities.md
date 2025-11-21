# Database Entities

The application uses TypeORM entities to map to the PostgreSQL database tables. Below is a comprehensive list of all entities in the system.

## User & Authentication

### User (`user.entity.ts`)
Represents a user in the system.
*   `id`: Primary Key.
*   `username`: Unique username.
*   `email`: Unique email address.
*   `password`: Hashed password.
*   `rol`: Relation to `RolEntity`.
*   `people`: Relation to `PeopleEntity` (Personal details).
*   `profilePhoto`: URL to profile photo.
*   `location`: User location data.
*   `verified`: Boolean indicating if the user is verified.
*   `block`: Boolean indicating if the user is blocked.
*   `loginAttempts`: Number of failed login attempts.
*   `lockUntil`: Timestamp until when the account is locked.
*   `lastLogin`: Timestamp of the last login.

### People (`people.entity.ts`)
Stores personal information linked to a user.
*   `id`: Primary Key.
*   `name`: First name.
*   `lastName`: Last name.
*   `dni`: Identification number.
*   `typeDni`: Relation to `TypeDniEntity`.
*   `birdthDate`: Date of birth.
*   `residencia`: Residential address.
*   `municipio`: Municipality/City data.
*   `telefono`: Phone number.
*   `supportId`: URL to support document (e.g., ID scan).

### Rol (`rol.entity.ts`)
Defines user roles (e.g., Admin, User).
*   `id`: Primary Key.
*   `rol`: Role name (unique).

### TypeDni (`type.dni.entity.ts`)
Defines types of identification documents.
*   `id`: Primary Key.
*   `type`: Document type name (unique).

## Posts & Content

### Post (`post.entity.ts`)
Represents a user's post (donation request or offer).
*   `id`: Primary Key.
*   `title`: Post title.
*   `message`: Post content/description.
*   `user`: Relation to `UserEntity` (Creator).
*   `typePost`: Relation to `TypePostEntity`.
*   `tags`: Relation to `PostTagEntity`.
*   `imagePost`: Relation to `ImagePostEntity`.
*   `postLiked`: Relation to `PostLikedEntity`.
*   `postArticle`: Relation to `PostArticleEntity`.

### TypePost (`type.port.entity.ts`)
Defines types of posts (e.g., Donation, Request).
*   `id`: Primary Key.
*   `type`: Post type name (unique).

### ImagePost (`image.post.entity.ts`)
Stores images associated with a post.
*   `id`: Primary Key.
*   `post`: Relation to `PostEntity`.
*   `image`: URL or path to the image.

### PostLiked (`post.liked.entity.ts`)
Tracks likes on posts.
*   `id`: Primary Key.
*   `post`: Relation to `PostEntity`.
*   `user`: Relation to `UserEntity`.

### Tags (`tags.entity.ts`)
Defines tags that can be applied to posts.
*   `id`: Primary Key.
*   `tag`: Tag name (unique).

### PostTag (`post.tags.entity.ts`)
Associates tags with posts (Many-to-Many relationship).
*   `id`: Primary Key.
*   `post`: Relation to `PostEntity`.
*   `tag`: Relation to `TagsEntity`.

## Donations

### Donation (`donation.entity.ts`)
Represents a donation transaction.
*   `id`: Primary Key.
*   `post`: Relation to `PostEntity`.
*   `user`: Relation to `UserEntity` (Donor).
*   `statusDonation`: Relation to `StatusDonationEntity`.
*   `lugarRecogida`: Pickup location.
*   `lugarDonacion`: Donation location.
*   `fechaMaximaEntrega`: Deadline for delivery.
*   `comments`: JSON field for additional comments.
*   `reviewwDonation`: Relation to `DonationReviewEntity`.
*   `chat`: Relation to `ChatEntity`.

### StatusDonation (`status.donation.entity.ts`)
Defines possible statuses for a donation.
*   `id`: Primary Key.
*   `status`: Status name (unique).

### DonationReview (`donation.review.entity.ts`)
Stores reviews for completed donations.
*   `id`: Primary Key.
*   `review`: Text content of the review.
*   `raiting`: Numerical rating (1-5).
*   `user`: Relation to `UserEntity` (Reviewer).
*   `donation`: Relation to `DonationEntity`.

## Articles & Inventory

### Article (`article.entity.ts`)
Represents a generic article or item type.
*   `id`: Primary Key.
*   `name`: Article name.
*   `descripcion`: Description.

### PostArticle (`postarticle.entity.ts`)
Associates articles with a post (items requested/offered).
*   `id`: Primary Key.
*   `article`: Relation to `ArticleEntity`.
*   `post`: Relation to `PostEntity`.
*   `quantity`: Quantity of the article.
*   `status`: Relation to `StatusPostDonationArticle`.

### PostArticleDonation (`post.article.donation.entity.ts`)
Tracks specific articles within a donation.
*   `id`: Primary Key.
*   `donation`: Relation to `DonationEntity`.
*   `postArticle`: Relation to `PostArticleEntity`.
*   `quantity`: Quantity donated.

### StatusPostDonationArticle (`status.postdonationarticle.entity.ts`)
Defines statuses for articles within a donation post.
*   `id`: Primary Key.
*   `status`: Status name.

### UserArticle (`useraticle.entity.ts`)
Associates articles with a user (e.g., user inventory or needs).
*   `id`: Primary Key.
*   `user`: Relation to `UserEntity`.
*   `article`: Relation to `ArticleEntity`.
*   `cant`: Quantity.
*   `needed`: Boolean indicating if it's a need.

## Chat & Messaging

### Chat (`chat.entity.ts`)
Represents a chat session.
*   `id`: Primary Key.
*   `chatName`: Name of the chat.
*   `chatStatus`: Relation to `ChatStatusEntity`.
*   `donation`: Relation to `DonationEntity`.
*   `userChat`: Relation to `UserChatEntity`.
*   `messageChat`: Relation to `MessageChatEntity`.

### ChatStatus (`chat.status.entity.ts`)
Defines statuses for a chat.
*   `id`: Primary Key.
*   `status`: Status name (unique).

### UserChat (`user.chat.entity.ts`)
Associates users with chat sessions.
*   `id`: Primary Key.
*   `user`: Relation to `UserEntity`.
*   `chat`: Relation to `ChatEntity`.
*   `donator`: Boolean indicating if the user is the donor.
*   `admin`: Boolean indicating if the user is an admin.

### MessageChat (`message.chat.entity.ts`)
Represents an individual message in a chat.
*   `id`: Primary Key.
*   `chat`: Relation to `ChatEntity`.
*   `user`: Relation to `UserEntity` (Sender).
*   `message`: Message content.
*   `type`: Relation to `TypeMessageEntity`.
*   `read`: Boolean indicating if the message has been read.

### TypeMessage (`type.message.entity.ts`)
Defines types of messages (e.g., text, image).
*   `id`: Primary Key.
*   `type`: Message type name (unique).

## Notifications & System

### Notify (`notify.entity.ts`)
Represents a system notification.
*   `id`: Primary Key.
*   `title`: Notification title.
*   `message`: Notification body.
*   `link`: Optional link.
*   `type`: Relation to `TypeNotifyEntity`.

### TypeNotify (`type.notify.entity.ts`)
Defines types of notifications.
*   `id`: Primary Key.
*   `type`: Notification type name (unique).

### UserNotify (`user.notify.entity.ts`)
Associates notifications with users (recipient tracking).
*   `id`: Primary Key.
*   `user`: Relation to `UserEntity`.
*   `notify`: Relation to `NotifyEntity`.
*   `read`: Boolean indicating if the notification has been read.

### System (`system.entity.ts`)
Stores system-wide configurations or legal texts.
*   `id`: Primary Key.
*   `termsAndConditions`: Terms and conditions text.
*   `privacyPolicy`: Privacy policy text.
*   `aboutUs`: About us text.

### UserSystem (`user.system.entity.ts`)
Associates users with system acceptances (e.g., terms accepted).
*   `id`: Primary Key.
*   `user`: Relation to `UserEntity`.
*   `system`: Relation to `systemEntity`.

### Audit (`audit.entity.ts`)
Logs system actions for auditing purposes.
*   `id`: Primary Key.
*   `user`: Relation to `UserEntity`.
*   `action`: Action performed.
*   `comment`: Additional details.
*   `status`: Status of the action.

### Report (`report.entity.ts`)
Represents a user report (e.g., reporting a post or user).
*   `id`: Primary Key.
*   `user`: Relation to `UserEntity` (Reporter).
*   `typeReport`: Relation to `TypeReportEntity`.
*   `comments`: Report details.

### TypeReport (`type.report.entity.ts`)
Defines types of reports.
*   `id`: Primary Key.
*   `type`: Report type name (unique).

### CommentSupportId (`comment.supportid.entity.ts`)
Stores comments related to support ID verification.
*   `id`: Primary Key.
*   `comment`: Comment text.
*   `user`: Relation to `UserEntity`.
*   `status`: Relation to `StatusSupportIdEntity`.
*   `processedBy`: ID of the admin who processed it.
*   `processedAt`: Timestamp of processing.
*   `rejectReason`: Reason for rejection.

### StatusSupportId (`status.supportid.entity.ts`)
Defines statuses for support ID verification.
*   `id`: Primary Key.
*   `name`: Status name.
