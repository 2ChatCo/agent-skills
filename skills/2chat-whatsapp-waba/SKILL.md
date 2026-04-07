---
name: 2chat-whatsapp-waba
description: Use this skill when the user wants to send WhatsApp messages via the Business API (WABA), list or use message templates, or subscribe to WABA webhook events via the 2Chat API. Trigger phrases include "send WABA message", "WhatsApp Business API", "WABA template", "list templates", "send template message", "WABA webhook".
---

# 2Chat — WhatsApp Business API (WABA)

Send template and session messages via the WhatsApp Business API (WABA) through the [2Chat API](https://developers.2chat.co/).

This is for numbers connected via the official WhatsApp Business API, not WhatsApp Web connections.

## Authentication

All requests require the header:

```
X-User-API-Key: <api_key>
```

API keys are generated at [app.2chat.io/developers](https://app.2chat.io/developers). Base URL: `https://api.p.2chat.io/open`

---

## Templates

### List templates
```http
GET /waba/templates?phone_number={waba_number}
```
Query params: `phone_number` (required, E.164 format), `page` (zero-based, default 0).

Returns templates with `uuid`, `name`, `status` (`APPROVED`, `PENDING`, `REJECTED`, `FAILED`), `category`, `template_content`, and associated `waba` number details.

Use the template `uuid` as `template_uuid` when sending template messages. Pagination via `next_page` (null when no more pages).

---

## Send Message

### Send a template or session message
```http
POST /waba/send-message
```

**Template message** (for initiating conversations or messaging outside the 24-hour window):
```json
{
  "to_number": "+595981048477",
  "from_number": "+5215512345432",
  "template_uuid": "TPL-uuid-from-list-templates",
  "params": {
    "body": ["value1", "value2"],
    "header": "Header value",
    "buttons": {}
  }
}
```
- `params` is required even if empty (`{}`)
- `params.body` replaces `{{1}}`, `{{2}}`, etc. in the template
- `params.header` fills the header variable if the template has one
- `params.buttons` provides dynamic URL or OTP payloads

**Session message** (within an active 24-hour conversation window):
```json
{
  "to_number": "+595981048477",
  "from_number": "+5215512345432",
  "text": "Plain text reply"
}
```

Returns: `success`, `message_uuid`, `batched`.

---

## Webhooks

### Subscribe to WABA events
```http
POST /webhooks/subscribe/{event_name}
```
```json
{
  "hook_url": "https://your-server.com/webhook",
  "on_number": "+5215512345432"
}
```

### Supported WABA events

| Event | Description |
|---|---|
| `whatsapp.waba.message.sent` | A message was sent |
| `whatsapp.waba.message.receipt.sent` | Message reached the WhatsApp network |
| `whatsapp.waba.message.receipt.received` | Message received by the recipient |

Returns the webhook subscription with `uuid`, `event_name`, `channel_uuid`, `hook_url`, and `created_at`.
