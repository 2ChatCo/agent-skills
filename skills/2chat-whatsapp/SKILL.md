---
name: 2chat-whatsapp
description: Use this skill when the user wants to connect/activate a WhatsApp number, send or read WhatsApp messages, check if a phone number is on WhatsApp, list conversations, manage connected numbers, set profile picture or status, or disconnect a WhatsApp number via the 2Chat API. Trigger phrases include "connect WhatsApp", "activate WhatsApp", "link WhatsApp number", "send a WhatsApp", "check WhatsApp number", "list my conversations", "read messages", "set WhatsApp status".
---

# 2Chat — WhatsApp Messaging

Send and receive WhatsApp messages, manage connected numbers, and update your WhatsApp profile via the [2Chat API](https://developers.2chat.co/).

## Authentication

All requests require the header:

```
X-User-API-Key: <api_key>
```

API keys are generated at [app.2chat.io/developers](https://app.2chat.io/developers). Base URL: `https://api.p.2chat.io/open`

---

## Numbers

### Create a WhatsApp Web channel
```http
POST /whatsapp/channel/create
```
```json
{
  "phone_number": "+14378375573",
  "friendly_name": "My Number"
}
```
Returns a channel object with `uuid` and `connection_status` (`D`=disconnected, `C`=connected). After creation the channel is disconnected and awaiting a QR scan.

### Get QR code for a channel
```http
GET /whatsapp/channel/{uuid}/qr-code
```
Returns `qr_code` (raw string) and `qr_code_image_url` (PNG URL). The QR refreshes at most every 5 seconds — don't poll faster than that. The user scans it from WhatsApp → Settings → Linked Devices → Link a device; once scanned, `connection_status` flips from `D` to `C`.

### List connected numbers
```http
GET /whatsapp/get-numbers
```
Query params: `status` (`connected`, `disconnected`, `all`), `page_number`, `results_per_page` (default 50).

Returns array of numbers with `uuid`, `phone_number`, `friendly_name`, `platform`, `channel_type`, `connection_status` (`C`=connected, `D`=disconnected, `F`=failure).

### Get number details
```http
GET /whatsapp/channel/{uuid}
```
Returns: connection status, timezone, language, business profile flag, pushname.

### Execute channel command
```http
POST /whatsapp/channel/{uuid}/{command}
```
Supported commands: `connect` (triggers QR code), `disconnect` (disconnects without deleting).

### Delete channel
```http
DELETE /whatsapp/channel/{uuid}
```

### Check if a number is on WhatsApp
```http
GET /whatsapp/check-number/{your-number}/{target-number}
```
Add `?extra-information=true` for carrier and business profile details (costs 2 API calls instead of 1).

Returns: `is_valid`, `on_whatsapp`, `number` (country, region, carrier, timezone), and `whatsapp_info` (profile pic, business status, verified name, status text) when on WhatsApp.

---

## Messaging

### Send a message
```http
POST /whatsapp/send-message
```
```json
{
  "to_number": "+15551234567",
  "from_number": "+15557654321",
  "text": "Hello from 2Chat!"
}
```

Use `to_group_uuid` instead of `to_number` to send to a group. Cannot use both simultaneously.

For media (max 16 MB, must be a publicly accessible URL):
```json
{
  "to_number": "+15551234567",
  "from_number": "+15557654321",
  "url": "https://example.com/image.jpg"
}
```

For a GPS pin:
```json
{
  "to_number": "+15551234567",
  "from_number": "+15557654321",
  "pin": {
    "latitude": "-25.7752926",
    "longitude": "-56.647703",
    "name": "Somewhere",
    "address": "Some address 123",
    "url": "https://example.com"
  }
}
```

Returns: `success`, `message_uuid`, `batched`.

### Get messages
```http
GET /whatsapp/messages/{your-number}
GET /whatsapp/messages/{your-number}/{remote-number}
```
Query param: `page_number` (100 messages per page). Only messages after connection are returned.

`sent_by` values: `api`, `agent`, `user`.

### Get a single message
```http
GET /whatsapp/message/{session_key}/{message_uuid}
```
Returns full message details including delivery status: `wa_msg_ack` (0=created, 1=sent, 2=received, 3=read).

### Delete a message
```http
DELETE /whatsapp/message/{session_key}/{message_uuid}
```
Only possible within 60 hours of sending. Group admins can delete any message; non-admins only their own.

### Get group messages
```http
GET /whatsapp/groups/messages/{group_uuid}
```
Query param: `page_number` (50 messages per page). Each message includes `participant` info (phone, pushname).

---

## Conversations

### List conversations
```http
GET /whatsapp/conversations/{channel_uuid}
```
Query params: `page_number` (10 per page), `phone_number` (search filter, 3-20 digits).

Returns sessions with contact info, last activity, profile picture, and group chat flag.

---

## Profile

### Set profile picture
```http
POST /whatsapp/set-profile-picture/{number}
```
```json
{ "url": "https://example.com/photo.jpg" }
```
Set `url` to `null` to remove. Returns: `success`, `batched`.

### Set status
```http
POST /whatsapp/set-status/{number}
```
```json
{ "status": "My cool new status" }
```
Returns: `success`, `batched`.

---

## Rate Limits

| Account type | Limit |
|---|---|
| Trial | 10 req/min (1 every 6s), max 100 verifications |
| Paid | Up to 50 req/min per number |

## Tips

- Always `GET /whatsapp/check-number` before sending to avoid delivery failures.
- Use `message_uuid` from send responses to track delivery via webhooks.
- Media URLs must be publicly accessible at send time.
- For bulk messaging, respect rate limits and check the `batched` flag.
