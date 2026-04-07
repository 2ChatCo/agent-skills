---
name: 2chat-sms
description: Use this skill when the user wants to send SMS text messages via the 2Chat API. Trigger phrases include "send SMS", "send text message", "SMS message", "2chat SMS".
---

# 2Chat — SMS

Send SMS messages via the [2Chat API](https://developers.2chat.co/).

## Authentication

All requests require the header:

```
X-User-API-Key: <api_key>
```

API keys are generated at [app.2chat.io/developers](https://app.2chat.io/developers). Base URL: `https://api.p.2chat.io/open`

---

## Send SMS
```http
POST /sms/send
```
```json
{
  "from_number": "+17137157533",
  "to_number": "+442079460958",
  "text": "Hello via SMS!"
}
```

- Phone numbers must be in E.164 international format (with `+` prefix)
- `from_number` must be an SMS-enabled number on your account
- Messages over 160 characters are split into multiple segments and billed accordingly
- A2P messaging to US numbers is not currently supported

**Success response (HTTP 202):**
```json
{
  "success": true,
  "message_uuid": "abc123-def456-ghi789"
}
```

**Error response:**
```json
{
  "error": true,
  "error_message": "Description of what went wrong"
}
```
