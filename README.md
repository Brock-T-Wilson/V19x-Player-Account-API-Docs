# V19x Player Account API Documentation 

![API](https://img.shields.io/badge/API-REST-blue)
![Docs](https://img.shields.io/badge/Docs-Complete-brightgreen)


The V19x Player Account API is a mock REST API designed to manage player accounts, authentication, session handling, and security features for a gaming platform.

This API simulates real-world account and identity systems used in modern gaming services. It includes endpoints for user creation, authentication, multi-factor authentication (MFA), session management, and security event tracking.

## Table of Contents

- [Quick Start](#quick-start)
- [Authentication](#authentication)
- [Authentication Flow](#authentication-flow)
- [Token Lifecycle](#token-lifecycle)
- [Endpoints Overview](#endpoints-overview)
- [POST /auth/login](#post-authlogin)
- [POST /users](#post-users)
- [GET /users/{userId}](#get-usersuserid)
- [DELETE /users/{userId}](#delete-usersuserid)
- [POST /auth/refresh-token](#post-authrefresh-token)
- [POST /users/{userId}/mfa/enable](#post-usersuseridmfaenable)
- [GET /users/{userId}/security-events](#get-security-events)
- [GET /users/{userId}/sessions](#get-usersuseridsessions)
- [DELETE /users/{userId}/sessions/{sessionId}](#delete-usersuseridsessionssessionid)
- [Data Models](#data-models)
- [Error Handling](#error-handling)
- [Rate Limiting](#rate-limiting)
- [Example Workflow](#example-workflow)

## Base URL

Production (example):
https://api.v19x.com

## Quick Start

### 1. Log in

POST /auth/login

### 2. Use your access token

Authorization: Bearer {access_token}

### 3. Make an authenticated request

GET /users/{userId}

## Authentication

The V19x Player Account API uses Bearer Token authentication for secured endpoints.

### Authorization Header

All protected endpoints require the following header:
Authorization: Bearer {access_token}

## Authentication Flow

The V19x Player Account API uses a token-based authentication system to secure protected endpoints.

### Step 1: User Login

The user submits their credentials to the login endpoint:

POST /auth/login

The API validates the credentials and returns:

- access_token
- refresh_token
- expires_in

### Step 2: Use Access Token

The access token must be included in all protected requests using the Authorization header:

Authorization: Bearer {access_token}

### Step 3: Access Protected Endpoints

Once authenticated, the access token can be used to interact with secured endpoints such as:

- GET /users/{userId}
- GET /users/{userId}/sessions
- POST /users/{userId}/mfa/enable

### Step 4: Refresh Token

When the access token expires, a new token can be obtained using:

POST /auth/refresh-token

This allows continued access without requiring the user to log in again.

## Token Lifecycle

Access tokens are short-lived and used to authenticate API requests.

### Access Token

- Issued during login (`POST /auth/login`)
- Used to authorize requests to protected endpoints
- Expires after a set duration (`expires_in`)

### Refresh Token

- Issued alongside the access token
- Used to obtain a new access token without re-authentication

### Token Renewal Flow

1. User logs in and receives access and refresh tokens  
2. Access token is used for API requests  
3. Access token expires  
4. Refresh token is used to generate a new access token (`POST /auth/refresh-token`)
 

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
| POST | `/users/{userId}/mfa/enable` | Enables multi-factor authentication for a user. |
| GET | `/users/{userId}/security-events` | Retrieves recent security events for a user. |
| GET | `/users/{userId}/sessions` | Lists active sessions for a user. |
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

### Example Request

```bash
curl -X POST "{{baseUrl}}/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "player@v19x.com",
    "password": "SecurePass123!"
  }'
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

**Path Parameters**

- userId (string): Unique identifier for the user

**Headers**

Authorization: Bearer {access_token}  
Content-Type: application/json

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
Content-Type: application/json

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
"method": "authenticator_app"
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
Content-Type: application/json

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

<a id="get-security-events"></a>
## GET /users/{userId}/security-events



### Request

**URL**  
GET /users/{userId}/security-events

**Example**  
GET /users/user_001/security-events

**Headers**  

Authorization: Bearer {access_token}  
Content-Type: application/json

### Response

200 OK

```json


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

**Path Parameters**

- userId (string): Unique identifier for the user  
- sessionId (string): Unique identifier for the session

**Headers**  

Authorization: Bearer {access_token}  
Content-Type: application/json

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

## Data Models

### User

Represents a player account in the V19x system.

```json
{
  "id": "user_001",
  "email": "player@v19x.com",
  "gamertag": "V19xPlayer01",
  "account_status": "active",
  "created_at": "2026-05-03T14:00:00Z"
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

## Example Workflow

The following example demonstrates a typical flow for using the V19x Player Account API.

### 1. Create a User

POST /users

Creates a new player account.

---

### 2. Authenticate User

POST /auth/login

Returns an access token and refresh token.

---

### 3. Access Protected Data

GET /users/{userId}

Uses the access token to retrieve account details.

---

### 4. Enable Multi-Factor Authentication

POST /users/{userId}/mfa/enable

Adds an extra layer of security to the account.

---

### 5. View Security Events

GET /users/{userId}/security-events

Displays recent account activity.

---

### 6. Manage Sessions

GET /users/{userId}/sessions  
DELETE /users/{userId}/sessions/{sessionId}

Allows viewing and revoking active sessions.


## Additional Resources

- [Troubleshooting Guide](docs/troubleshooting.md)

## Version

v1.0

_Last updated: May 2026_



