# Interceptors

Interceptors have a set of useful capabilities which are inspired by the Aspect Oriented Programming (AOP) technique.

## AddRefreshTokenInterceptor

*   **Path**: `src/shared/interceptors/add-refresh-token.interceptor.ts`
*   **Purpose**: Intercepts the response to attach a refresh token.
*   **Usage**: Typically used in login or token refresh endpoints.
*   **Mechanism**:
    1.  Intercepts the outgoing response.
    2.  Extracts the refresh token from the result.
    3.  Sets it as an HTTP-only cookie or in the response header, depending on the implementation.
