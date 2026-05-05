# V19x Troubleshooting Guide

This guide helps resolve common issues related to account access, authentication, and session management in the V19x platform.

---

## Issue: Unable to Log In

### Symptoms

* Login request returns **401 Unauthorized**
* Error message: "Invalid email or password"

### Possible Causes

* Incorrect email or password
* Account does not exist
* Password reset has not been completed

### Solution

1. Verify that the email and password are entered correctly
2. Reset the password if necessary
3. Ensure the account has been created using `POST /users`
4. Attempt to log in again using `POST /auth/login`

---

## Issue: Access Token Expired

### Symptoms

* API requests return **401 Unauthorized**
* Error message: "Missing or invalid access token"

### Possible Causes

* Access token has expired (`expires_in` limit reached)

### Solution

1. Generate a new access token using `POST /auth/refresh-token`

2. Update the Authorization header:

   ```
   Authorization: Bearer {access_token}
   ```

3. Retry the request

---

## Issue: Suspicious or Unknown Sessions

### Symptoms

* Unknown device appears in active sessions
* Unexpected login activity

### Possible Causes

* Account accessed from another device
* Session not properly revoked

### Solution

1. Review active sessions using `GET /users/{userId}/sessions`
2. Identify any unfamiliar sessions
3. Revoke the session using `DELETE /users/{userId}/sessions/{sessionId}`
4. Enable multi-factor authentication using `POST /users/{userId}/mfa/enable`

---

## Issue: Rate Limit Exceeded

### Symptoms

* API requests return **429 Too Many Requests**
* Error message: "Too many requests. Please try again later."

### Possible Causes

* Too many requests sent within a short time period

### Solution

1. Wait before making additional requests
2. Reduce the frequency of API calls
3. Ensure requests stay within the rate limit (100 requests per minute per user)

---

## Additional Notes

* Always include a valid access token for protected endpoints
* Ensure request headers are properly formatted
* Verify endpoint URLs and parameters before making requests

