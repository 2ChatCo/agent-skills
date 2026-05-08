---
name: 2chat-sms
description: Use this skill when the user wants to send SMS text messages or list SMS channels (SMS-enabled numbers) on their 2Chat account. Trigger phrases include "send SMS", "send text message", "SMS message", "2chat SMS", "list SMS channels", "SMS numbers", "my SMS channels".
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

## List SMS channels
```http
GET /sms/channels
```
List the SMS channels (SMS-enabled numbers) available on the authenticated account. Use this to discover valid `from_number` values before calling `POST /sms/send`.

No query or body params.

**Success response (HTTP 200):**
```json
{
  "success": true,
  "data": {
    "channels": [
      {
        "uuid": "5f3c1a2b-9d4e-4a7c-8b21-7e6f2d8c1a90",
        "account_uuid": "ACC5150c8fe-a7e8-4212-abd1-531e4bbb4369",
        "voip_number_setup_uuid": "b1d2e3f4-5678-49ab-9cde-0123456789ab",
        "phone_number": "+17137157533",
        "friendly_name": "Houston SMS line",
        "iso_country_code": "US",
        "supports_sms_in": true,
        "supports_sms_out": true,
        "enabled": true,
        "connection_status": "C",
        "created_at": "2025-08-14T10:22:31.000000Z",
        "updated_at": "2025-08-14T10:22:31.000000Z"
      }
    ]
  }
}
```

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
