# Controllers (API Endpoints)

Controllers are the entry points for the API. They handle incoming HTTP requests, validate data, and delegate business logic to services. Below is a detailed breakdown of the available endpoints, grouped by their functional module and access level.

## Authentication (`Auth`)
**Controller**: `auth.controller.ts`
**Purpose**: Manages user registration, login, and account verification.

| Endpoint | Method | Description | Auth Required |
| :--- | :--- | :--- | :--- |
| `/auth/login` | `POST` | Authenticates a user with email and password. Returns a JWT `access_token` for subsequent requests. | No |
| `/auth/register` | `POST` | Registers a new user in the system. Requires personal information and location data. | No |
| `/auth/verify-email-token` | `POST` | Verifies a user's email address using a token sent via email. | No |
| `/auth/verify-email-code` | `POST` | Verifies a user's email address using a numeric code. | No |
| `/auth/forgot-password` | `POST` | Initiates the password recovery process by sending a reset link/code to the user's email. | No |

---

## Users (`User`)
**Controller**: `user.controller.ts`
**Purpose**: Handles user profile management, retrieval, and administrative actions.

###  Public / Shared
Endpoints accessible to everyone or authenticated users without special roles.

*   **Get User Profile (Detailed)**
    *   `GET /user/:id`
    *   Retrieves full public profile details for a specific user by their ID.
*   **Get User Profile (By Username)**
    *   `GET /user/username/:username`
    *   Retrieves profile details using the unique username.
*   **Get Minimal Profile**
    *   `GET /user/minimal/:id`
    *   Returns a simplified version of the user profile (name, photo, basic info).
*   **List Organizations**
    *   `GET /user/minimal/all/organizations`
    *   Retrieves a paginated list of registered organizations. Useful for directories or search.

###  Admin Only
Endpoints restricted to users with the `ADMIN` role.

*   **List All Users**
    *   `GET /user`
    *   Retrieves a paginated list of all users in the system.
*   **Create User (Admin)**
    *   `POST /user`
    *   Allows an admin to manually create a new user account.
*   **Update User (Admin)**
    *   `POST /user/update/:id`
    *   Updates any user's profile data.
*   **Delete User**
    *   `DELETE /user/delete/:id`
    *   Permanently removes a user from the system.
*   **Change User Role**
    *   `POST /user/change-role/:id`
    *   Promotes or demotes a user (e.g., User to Admin).
*   **Block/Unblock User**
    *   `POST /user/change-block-status/:id`
    *   Suspends or restores a user's access to the platform.
*   **Review Support Documents**
    *   `GET /user/upload-support`
    *   Lists users who have uploaded verification documents for review.

---

## Posts (`Post`)
**Controller**: `post.controller.ts`
**Purpose**: Manages donation requests and offers (posts).

###  User (My Posts)
Endpoints for managing the current user's content.

*   **Create Post**
    *   `POST /post/create`
    *   Creates a new donation request or offer. Supports image uploads.
*   **My Posts**
    *   `GET /post/me/posts`
    *   Lists all posts created by the logged-in user.
*   **Update Post**
    *   `POST /post/update/:id`
    *   Edits the title, description, or details of a post.
*   **Delete Post**
    *   `DELETE /post/delete/:id`
    *   Removes a post created by the user.
*   **Manage Images**
    *   `POST /post/image/add/:postId`: Uploads additional images to a post.
    *   `DELETE /post/image/delete/:imageId/post/:postId`: Removes a specific image.
*   **Manage Tags**
    *   `POST /post/add/tag/:tagId/post/:postId`: Adds a tag (e.g., "Food", "Clothes") to a post.
    *   `DELETE /post/remove/tag/:tagId/post/:postId`: Removes a tag.

###  Public
Endpoints for viewing posts.

*   **Feed / All Posts**
    *   `GET /post/all`
    *   Retrieves the main feed of posts with pagination.
*   **Filter Posts**
    *   `POST /post/all/filters`
    *   Advanced search for posts based on categories, location, or keywords.
*   **Post Details**
    *   `GET /post/:id`
    *   Retrieves the full details of a single post.
*   **User's Posts**
    *   `GET /post/user/:userId`
    *   Lists all posts belonging to a specific user.

###  Admin Only
*   **Admin Update/Delete**: Admins can update (`POST /post/update/admin/:id`) or delete (`DELETE /post/admin/delete/:id`) any post for moderation.
*   **Admin Tag/Image Management**: Admins can add/remove tags and images from any post.

---

## Donations (`Donation`)
**Controller**: `donation.controller.ts`
**Purpose**: Manages the donation process, from initiation to completion.

*   **Create Donation**
    *   `POST /donation`
    *   Initiates a donation offer for a specific post.
*   **My Donations (As Donor)**
    *   `GET /donation/user`
    *   Lists donations made by the current user.
*   **Update Status**
    *   `POST /donation/:id/status`
    *   Updates the status of a donation (e.g., `PENDING` -> `ACCEPTED`).
*   **Admin Status Update**
    *   `POST /donation/:id/status/admin`
    *   Allows admins to force a status change on a donation.

---

## Statistics (`Statistics`)
**Controller**: `statistics.controller.ts`
**Purpose**: Provides analytics and ranking data.

*   **User Metrics**: `GET /statistics/user/:id` - Stats for a specific user (donations made/received).
*   **Rankings**:
    *   `GET /statistics/users/rankings`: General user rankings.
    *   `GET /statistics/users/rankings/donations-made`: Top donors.
    *   `GET /statistics/users/rankings/donations-received`: Top beneficiaries.
    *   `GET /statistics/users/rankings/average-rating`: Highest rated users.
*   **Public Stats**: `GET /statistics/public` - General platform statistics.

---

## Artificial Intelligence (`IA`)
**Controller**: `ia.controller.ts`
**Purpose**: AI-powered features.

*   **Generate Tags from Image**
    *   `POST /ia/tags-from-images`
    *   **Input**: Image file(s).
    *   **Output**: A list of descriptive tags (e.g., `["Laptop", "Electronics", "Used"]`).
    *   **Logic**: Uses **Google Gemini** (primary) or **AWS Rekognition** (backup) to analyze the image.

---

## Location (`Countries`)
**Controller**: `countries.controller.ts`
**Purpose**: Provides geographic data (Countries, States, Cities).

*   **List Countries**: `GET /countries`
*   **Get Country**: `GET /countries/iso/:iso` or `GET /countries/id/:id`
*   **List States**: `GET /countries/states/iso/:iso` (By Country ISO)
*   **List Cities**: `GET /countries/countries/:iso/states/:stateIso/cities` (By State)

---

## Likes (`PostLiked`)
**Controller**: `postLiked.controller.ts`
**Purpose**: Manages likes on posts.

*   **List Likes**: `GET /postliked/userslike/:postId` - See who liked a post.
*   **Add Like**: `POST /postliked/addlike/:postId` - Like a post.
*   **Remove Like**: `DELETE /postliked/removelike/:postId` - Unlike a post.
