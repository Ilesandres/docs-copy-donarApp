# Configuration and Environment Variables

The backend application uses environment variables for configuration, including database connections, JWT settings, email services, and external API integrations.

## Environment Variables

The `.env` file should be created in the root of the backend project. You can use `.env.example` as a template.

### General
*   `APP_PORT`: Port where the application will run (e.g., `5000`).

### Database (PostgreSQL)
*   `DB_HOST`: Database host (e.g., `localhost`).
*   `DB_PORT`: Database port (e.g., `5432`).
*   `DB_USER`: Database username.
*   `DB_PASS`: Database password.
*   `DB_NAME`: Database name.

### Authentication (JWT)
*   `JWT_SECRET`: Secret key for signing JWT tokens.
*   `JWT_EXPIRES_IN`: Token expiration time (e.g., `15m`).

### External APIs

#### Countries API (Location)
Used to fetch countries, states, and cities data.
*   `URL_COUNTRIES_API`: Base URL for the Countries API.
*   `API_KEY_COUNTRIES_API`: API Key for authentication.

#### Sentiment Analysis (Pysentimiento)
Used to analyze the sentiment of donation reviews and assign a rating (0-5).
*   `MICROSERVICONSENTIMENTS_URL`: Primary URL for the sentiment analysis microservice.
*   `MICROSERVICONSENTIMENTS_URL_AUX`: Auxiliary URL for the sentiment analysis microservice (fallback).

### Email Service
*   `MAIL_HOST`: SMTP host.
*   `MAIL_USER`: SMTP username.
*   `MAIL_PASSWORD`: SMTP password.
*   `MAIL_FROM`: Sender email address.
*   `MAIL_PORT`: SMTP port.

### Frontend Integration
*   `URL_FRONTEND`: Base URL of the frontend application.
*   `URL_FRONTEND_VERIFY_TOKEN`: Path for email token verification.
*   `URL_FRONTEND_VERIFY`: Path for email verification.
*   `URL_FRONTEND_RESET_PASS_TOKEN`: Path for password reset token.

### Cloudinary (File Uploads)
*   `CLOUDINARY_NAME`: Cloud name.
*   `CLOUDINARY_API_KEY`: API Key.
*   `CLOUDINARY_API_SECRET`: API Secret.
*   `CLOUDINARY_FOLDER_BASE`: Base folder for uploads.
*   `CLOUDINARY_PROFILE_FOLDER`: Folder for profile photos.
*   `CLOUDINARY_DOCS_FOLDER`: Folder for documents.
*   `CLOUDINARY_CHATS_FOLDER`: Folder for chat images.
*   `CLOUDINARY_POST_FOLDER`: Folder for post images.

### AWS (Optional / Backup AI)
Used as a fallback for image processing (AWS Rekognition) if the primary AI service (Gemini) fails.
*   `AWS_REGION`: AWS Region.
*   `AWS_ACCESS_KEY_ID`: AWS Access Key ID.
*   `AWS_SECRET_ACCESS_KEY`: AWS Secret Access Key.

### AI Integration
*   `GEMINI_API_KEY`: API Key for Gemini (if used).

## External API Integrations

### Country State City API
The application integrates with an external API to provide location data (Countries, States, Cities). This is handled by the `CountriesService`.
*   **Official Documentation**: [https://docs.countrystatecity.in/](https://docs.countrystatecity.in/)
*   **Usage**: Registration, Profile updates.

### Pysentimiento (Sentiment Analysis)
The application uses a microservice ( Python-based using `pysentimiento`) to analyze the text of donation reviews.
*   **Usage**: When a user leaves a review for a donation (`DonationReviewService`).
*   **Function**: Analyzes the review text and returns a sentiment score (converted to a 1-5 rating).
