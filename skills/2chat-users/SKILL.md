---
name: 2chat-users
description: Use this skill when the user wants to list the users in their 2Chat account — for example to discover user UUIDs when minting per-user JWTs for the SDK. Trigger phrases include "list users", "list account users", "get my users", "show users in my account", "user uuid", "2chat users".
---

# 2Chat — Users

List the users on your 2Chat account via the [2Chat API](https://developers.2chat.co/).

## Authentication

All requests require the header:

```
X-User-API-Key: <api_key>
```

API keys are generated at [app.2chat.io/developers](https://app.2chat.io/developers). Base URL: `https://api.p.2chat.io/open`

---

## List account users
```http
GET /users
```
Returns the active users on the account. Useful for discovering a user's `uuid` when minting per-user JWTs for the SDK.

No query or body parameters.

**Success response (HTTP 200):**
```json
{
  "success": true,
  "data": {
    "users": [
      {
        "uuid": "USR-3f8c1d2a-9b4e-4a7f-8c1d-2a9b4e4a7f8c",
        "first_name": "Ada",
        "last_name": "Lovelace",
        "email": "ada@example.com"
      }
    ]
  }
}
```

Only users with `ACTIVE` status are returned. Invited, suspended, or deleted users are omitted.
