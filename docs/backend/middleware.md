# Middleware

Middleware is a function which is called before the route handler.

## RefreshTokenMiddleware

*   **Path**: `src/shared/middleware/refresh-token.middleware.ts`
*   **Purpose**: Handles the extraction and validation of refresh tokens from incoming requests.
*   **Usage**: Applied to routes that require token refreshing.
*   **Mechanism**:
    1.  Checks for the presence of a refresh token in cookies or headers.
    2.  Validates the token format.
    3.  Attaches the token payload or the token itself to the request object for downstream use.
