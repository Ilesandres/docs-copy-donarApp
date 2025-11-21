# Backend Modules

The application is divided into several feature modules. Each module encapsulates specific functionality, often mapping to database entities or specific business logic domains.

## Module Overview

| Module | Description | Category |
| :--- | :--- | :--- |
| **Article** | Manages generic articles or items. | Feature |
| **Audit** | Handles system auditing, tracking user actions and system events. | Core |
| **Auth** | Authentication and authorization, managing login, registration, and JWT tokens. | Core |
| **Chat** | Real-time chat functionality using WebSockets. | Core |
| **ChatStatus** | Manages statuses for chat sessions. | Helper |
| **CommentSupportId** | Manages comments related to support ID verification. | Helper |
| **Countries** | Manages location data (Countries, States, Cities) using an external API. | Core |
| **Donation** | Manages donations, including tracking status and history. | Core |
| **DonationReview** | Manages reviews for donations, integrating with **Pysentimiento**. | Feature |
| **ImagePost** | Manages images associated with posts. | Helper |
| **MessageChat** | Handles individual chat messages. | Feature |
| **Notify** | Notification system for sending alerts to users. | Core |
| **People** | Manages personal information profiles linked to users. | Feature |
| **Post** | Handles user posts, including creation and interactions. | Core |
| **PostArticle** | Manages articles associated with a post. | Helper |
| **PostDonationArticle** | Manages the relationship between donations and articles (items donated). | Helper |
| **PostLiked** | Tracks likes on posts. | Feature |
| **PostTags** | Manages tags associated with posts. | Helper |
| **Report** | Reporting system for content or user issues. | Core |
| **Rol** | Role management, defining permissions for different user types. | Core |
| **Statistics** | System statistics and analytics. | Core |
| **StatusArticleDonation** | Manages statuses for articles within a donation. | Helper |
| **StatusDonation** | Manages statuses for donations. | Helper |
| **StatusSupportId** | Manages statuses for support ID verification. | Helper |
| **System** | System configurations and settings. | Core |
| **Tags** | Manages available tags for posts. | Feature |
| **TypeDni** | Manages types of identification documents. | Helper |
| **TypeMessage** | Manages types of chat messages. | Helper |
| **TypeNotify** | Manages types of notifications. | Helper |
| **TypePost** | Manages types of posts. | Helper |
| **TypeReport** | Manages types of reports. | Helper |
| **User** | User management, profile updates, and user retrieval. | Core |
| **UserArticle** | Manages articles associated with a user (inventory/needs). | Helper |
| **UserChat** | Manages the relationship between users and chats. | Helper |
| **UserNotify** | Manages the relationship between users and notifications. | Helper |
| **UserSystem** | Manages user acceptance of system terms/policies. | Helper |

## Module Structure

Each module typically contains:
- `*.module.ts`: The module definition file.
- `*.controller.ts`: The API controller.
- `*.service.ts`: The business logic service.
- `dto/`: Data Transfer Objects for validation.
- `entity/`: Database entities.
