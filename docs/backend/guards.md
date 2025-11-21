# Guards

Guards are responsible for authorizing requests based on certain conditions (permissions, roles, ACLs, etc.).

## JwtAuthGuard

*   **Path**: `src/shared/guards/jwt-auth.guard.ts`
*   **Purpose**: Ensures that the request contains a valid JWT token.
*   **Usage**: Applied to endpoints that require authentication.
*   **Mechanism**: Extends `AuthGuard('jwt')` from `@nestjs/passport`. It validates the token found in the `Authorization` header.

## RolesGuard

*   **Path**: `src/shared/guards/roles.guard.ts`
*   **Purpose**: Restricts access to endpoints based on user roles.
*   **Usage**: Used in conjunction with the `@Roles()` decorator.
*   **Mechanism**:
    1.  Retrieves required roles from the route handler metadata.
    2.  Checks if the authenticated user (attached to the request) has one of the required roles.
    3.  Returns `true` if authorized, `false` otherwise.

## WsRefreshGuard

*   **Path**: `src/shared/guards/ws-refresh.guard.ts`
*   **Purpose**: Specialized guard for WebSocket connections or specific refresh scenarios.
*   **Usage**: Protects WebSocket gateways or refresh endpoints.
