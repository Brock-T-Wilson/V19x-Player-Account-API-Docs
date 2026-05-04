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
```

**Response**

200 OK
```JSON
{
  "access_token": "v19x_access_abc123",
  "refresh_token": "v19x_refresh_xyz789",
  "token_type": "Bearer",
  "expires_in": 3600,
  "user": {
    "id": "user_001",
    "gamertag": "V19xPlayer01"
  }
}
```

**Error Response**

401 Unauthorized
```JSON
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid email or password."
  }
}
```
## POST /users

Creates a new player account.

### Request

**URL**

POST /users

**Headers**

Content-Type: application/json

**Body**

```json
{
"email": "player@v19x.com",
"gamertag": "V19xPlayer01",
"password": "SecurePass123!"
}
```

**Response**

201 Created

```json
{
  "id": "user_001",
  "email": "player@v19x.com",
  "gamertag": "V19xPlayer01",
  "account_status": "active",
  "created_at": "2026-05-03T14:00:00Z"
}
```

**Error Response**

400 Bad Request

```json
{
  "error": {
    "code": "BAD_REQUEST",
    "message": "Invalid input data."
  }
}
```

## GET /users/{userId}

Retrieves a player account by user ID.

### Request

**URL**

GET /users/user_001

**Headers**

Authorization: Bearer {access_token}

### Response

200 OK

```json
{
"id": "user_001",
"email": "player@v19x.com",
"gamertag": "V19xPlayer01",
"account_status": "active",
"created_at": "2026-05-03T14:00:00Z"
}
```

**Error Response**

404 Not Found

```json
{
  "error": {
    "code": "NOT_FOUND",
    "message": "User not found."
  }
}
```

## DELETE /users/{userId}

Deactivates a player account by user ID.

### Request

**URL**

DELETE /users/{userId}

**Example**

DELETE /users/user_001

**Headers**

Authorization: Bearer {access_token}

### Response

200 OK

```json
{
"message": "User account deactivated successfully.",
"user_id": "user_001"
}
```

**Error Response**

```json
{
  "error": {
    "code": "NOT_FOUND",
    "message": "User not found."
  }
}
```

## POST /auth/refresh-token

Generates a new access token using a valid refresh token.

### Request

**URL**

POST /auth/refresh-token

**Headers**

Content-Type: application/json

**Body**

```json
{
"refresh_token": "v19x_refresh_xyz789"
}
```

**Response**

200 OK

```json
{
  "access_token": "v19x_access_new456",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

**Error Response**

401 Unauthorized

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid or expired refresh token."
  }
}
```

## POST /users/{userId}/mfa/enable

Enables multi-factor authentication for a player account.

### Request

**URL**

POST /users/{userId}/mfa/enable

**Example**

POST /users/user_001/mfa/enable

**Headers**

Authorization: Bearer {access_token}
Content-Type: application/json

**Body**

```json
{
"method": "authenticator_app",
}
```

**Response**

200 OK

```json
{
  "mfa_enabled": true,
  "method": "authenticator_app",
  "recovery_codes": [
    "V19X-94KD-21LA",
    "V19X-83JS-10QP"
  ]
}
```

**Error Response**

401 Unauthorized

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Missing or invalid access token."
  }
}
```
## GET /users/{userId}/sessions

Lists active sessions for a player account.

### Request

**URL**

GET /users/{userId}/sessions

**Example**

GET /users/user_001/sessions

**Headers**

Authorization: Bearer {access_token}

### Response

200 OK

```json
{
  "user_id": "user_001",
  "events": [
    {
      "event_id": "evt_001",
      "type": "login_success",
      "ip_address": "192.0.2.10",
      "device": "V19x Console",
      "created_at": "2026-05-03T13:45:00Z"
    },
    {
      "event_id": "evt_002",
      "type": "mfa_enabled",
      "ip_address": "192.0.2.11",
      "device": "Mobile App",
      "created_at": "2026-05-03T14:15:00Z"
    }
  ]
}
```

**Error Response**

401 Unauthorized

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Missing or invalid access token."
  }
}
```

## GET /users/{userId}/sessions

Lists active sessions for a player account.

### Request

**URL**  
GET /users/{userId}/sessions

**Example**  
GET /users/user_001/sessions

**Headers**  
Authorization: Bearer {access_token}

### Response

200 OK

```json
{
  "user_id": "user_001",
  "sessions": [
    {
      "session_id": "sess_12345",
      "device": "V19x Console",
      "location": "United States",
      "last_active": "2026-05-03T13:55:00Z"
    },
    {
      "session_id": "sess_67890",
      "device": "Mobile App",
      "location": "United States",
      "last_active": "2026-05-03T14:05:00Z"
    }
  ]
}
```
**Error Response**

401 Unauthorized

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Missing or invalid access token."
  }
}
```

## DELETE /users/{userId}/sessions/{sessionId}

Revokes an active session for a player account.

### Request

**URL**  
DELETE /users/{userId}/sessions/{sessionId}

**Example**  
DELETE /users/user_001/sessions/sess_12345

**Headers**  
Authorization: Bearer {access_token}

### Response

200 OK

```json
{
  "message": "Session revoked successfully.",
  "session_id": "sess_12345"
}
```

**Error Response**

401 Unauthorized

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Missing or invalid access token."
  }
}
```
## Error Handling

The V19x Player Account API uses standard HTTP status codes to indicate success or failure of API requests.

### Common Status Codes

| Status Code | Description |
|---|---|
| 200 OK | The request was successful. |
| 201 Created | A new resource was successfully created. |
| 400 Bad Request | The request was invalid or malformed. |
| 401 Unauthorized | Authentication is required or failed. |
| 404 Not Found | The requested resource does not exist. |
| 429 Too Many Requests | Rate limit exceeded. |

### Error Response Format

All error responses follow a consistent structure:

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Description of the error."
  }
}
```

**Example Error**

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Missing or invalid access token."
  }
}
```

## Rate Limiting

To ensure system stability and prevent abuse, the V19x Player Account API enforces rate limits on incoming requests.

### Limits

- 100 requests per minute per user

Rate limits are applied per authenticated user and reset every minute.


### Exceeding Rate Limits

If the rate limit is exceeded, the API returns:

**429 Too Many Requests**

```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests. Please try again later."
  }
}
```








