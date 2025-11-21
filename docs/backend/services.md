# Services

Services contain the business logic of the application. They interact with the database via repositories and are injected into controllers.

## Key Services

### AuthService (`auth.service.ts`)
*   **validateUser(email, pass)**: Verifies if the user exists and if the password matches the hash.
*   **login(user)**: Generates `accessToken` and `refreshToken` for a validated user.
*   **register(dto)**: Hashes the password and creates a new user record via `UserService`.

# Services

Services contain the business logic of the application. They interact with the database via repositories and are injected into controllers.

## Key Services

### AuthService (`auth.service.ts`)
*   **validateUser(email, pass)**: Verifies if the user exists and if the password matches the hash.
*   **login(user)**: Generates `accessToken` and `refreshToken` for a validated user.
*   **register(dto)**: Hashes the password and creates a new user record via `UserService`.

### UserService (`user.service.ts`)
*   **findAll()**: Returns all users from the database.
*   **findOne(id)**: Finds a user by ID.
*   **create(dto)**: Creates a new user entity and saves it.
*   **update(id, dto)**: Updates an existing user.

### Donation Service (`donation.service.ts`)

*   **Logic**:
    *   `create`: Creates a new donation, validating the post and articles.
    *   `updateStatus`: Updates the status of a donation (e.g., pending, accepted, rejected).
    *   `findByUser`: Retrieves donations for a specific user.

### Donation Review Service (`donationreview.service.ts`)

*   **Logic**:
    *   `createReview`: Allows a beneficiary to review a donation.
    *   **Sentiment Analysis**: Integrates with a **Pysentimiento** microservice to analyze the review text. It sends the text to the microservice and receives a sentiment score (converted to a 1-5 rating), which is then saved with the review.
    *   `getReviewsByDonationId`: Retrieves reviews for a specific donation.

### Countries Service (`countries.service.ts`)

*   **Logic**:
    *   **External API**: Connects to an external **Country State City API** ([https://docs.countrystatecity.in/](https://docs.countrystatecity.in/)) to fetch location data.
    *   `getCountriesData`: Fetches and caches (conceptually) the list of countries.
    *   `getStatesByCountry`: Fetches states for a given country ISO code.
    *   `getCitiesByState`: Fetches cities for a given state and country.
    *   Uses `URL_COUNTRIES_API` and `API_KEY_COUNTRIES_API` from configuration.

### AI Service (`ia.service.ts`)

*   **Logic**:
    *   **Image Analysis**: Uses **Google Gemini AI** (`gemini-2.5-flash`) to analyze uploaded images.
    *   `getTagsFromImages`: Accepts image files, sends them to Gemini with a prompt to generate 5 descriptive tags, and returns these tags as a list of strings.
    *   **Fallback**: If Gemini fails, it attempts to use **AWS Rekognition** (via `RespaldoiaService`) to detect labels in the images, ensuring high availability for image processing features.
