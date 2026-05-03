# V19x Player Account API Documentation 

The V19x Player Account API is a mock REST API designed to manage player accounts, authentication, session handling, and security features for a gaming platform.

This API simulates real-world account and identity systems used in modern gaming services. It includes endpoints for user creation, authentication, multi-factor authentication (MFA), session management, and security event tracking.

## Base URL

Production (example):
https://api.v19x.com

## Authentication

The V19x Player Account API uses Bearer Token authentication for secured endpoints.

### Authorization Header

All protected endpoints require the following header:
Authorization: Bearer {access_token} 

### Example

Authorization: Bearer v19x_access_abc123

### How to Obtain an Access Token

Access tokens are issued when a user successfully logs in using the login endpoint:

POST /auth/login

The response will include:

- access_token
- refresh_token
- expires_in

## Endpoints Overview

| Method | Endpoint | Description |
|---|---|---|
| POST | `/auth/login` | Authenticates a user and returns access and refresh tokens. |
| POST | `/users` | Creates a new player account. |
| GET | `/users/{userId}` | Retrieves a player account by user ID. |
| DELETE | `/users/{userId}` | Deactivates a player account. | 
| POST | `/auth/refresh-token` | Issues a new access token using a refresh token. |
| POST | `/users/{userId}/mfa/enable` | Enables mutli-factor authentication for a user. |
| GET | `/users/{userId}/security-events` | Retrieves recent security events for a user. |
| GET | `/users{userId}/sessions` | Lists active sessions for a user. |
| DELETE | `/users/{userId}/sessions/{sessionId}` | Revokes a specific user session. |

## POST /auth/login

Authenticates a user and returns access and refresh tokens.

### Request

**URL**
POST /auth/login

**Headers**
Content-Type: application/json

**Body**
```json
{
"email": "player@v19x.com",
"password": "SecurePass123!"
}
