---
name: 2chat-sms
description: Use this skill when the user wants to send SMS text messages, or read their SMS numbers, conversations and messages, via the 2Chat API. Trigger phrases include "send SMS", "send text message", "SMS message", "2chat SMS", "list SMS numbers", "list my SMS channels", "list SMS conversations", "read SMS messages", "get SMS thread", "get SMS message".
---

# 2Chat — SMS

Send and read SMS messages via the [2Chat API](https://developers.2chat.co/).

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

---

## Read SMS

Reading endpoints let you list your SMS numbers (channels), browse conversations per number, read a
full thread with a contact, and fetch a single message.

Notes that apply to all read endpoints:

- Ownership is always enforced against your account. An unknown or foreign UUID returns **404** (never 403), so existence is never leaked.
- SMS channel UUIDs start with `SMS`. Inbound message UUIDs start with `SMI`, outbound with `SMO`.
- Contact numbers are matched in E.164 form; a stored number with or without the leading `+` is reconciled automatically.

### List SMS channels (numbers)
```http
GET /sms/channels
```
Lists the SMS numbers on your account.

Query params: `page_number` (optional) — zero-based page index (default `0`), `results_per_page` (optional) — page size 1–200 (default `200`).

**Success response (HTTP 200):**
```json
{
  "success": true,
  "channels": [
    {
      "uuid": "SMS9f2c1a7b-4d3e-4c1a-9b6d-2e7f8a0c1d2e",
      "account_uuid": "ACC4b8e2d10-1f6a-4c3b-8d2e-9a7c6b5f4e31",
      "voip_number_setup_uuid": "VNSa1b2c3d4-5e6f-4a7b-8c9d-0e1f2a3b4c5d",
      "phone_number": "+17135550182",
      "friendly_name": "Sales line",
      "iso_country_code": "US",
      "supports_sms_in": true,
      "supports_sms_out": true,
      "enabled": true,
      "created_at": "2026-01-14T09:32:11Z",
      "updated_at": "2026-04-02T18:05:44Z"
    }
  ],
  "count": 1,
  "page": 0
}
```
`count` is the total number of channels on the account, not the size of the current page.

### Get a single SMS channel
```http
GET /sms/channel/{channel_uuid}
```
Returns the details/status of one SMS channel you own. `channel_uuid` must start with `SMS`.

**Success response (HTTP 200):**
```json
{
  "success": true,
  "channel": {
    "uuid": "SMS9f2c1a7b-4d3e-4c1a-9b6d-2e7f8a0c1d2e",
    "account_uuid": "ACC4b8e2d10-1f6a-4c3b-8d2e-9a7c6b5f4e31",
    "voip_number_setup_uuid": "VNSa1b2c3d4-5e6f-4a7b-8c9d-0e1f2a3b4c5d",
    "phone_number": "+17135550182",
    "friendly_name": "Sales line",
    "iso_country_code": "US",
    "supports_sms_in": true,
    "supports_sms_out": true,
    "enabled": true,
    "created_at": "2026-01-14T09:32:11Z",
    "updated_at": "2026-04-02T18:05:44Z"
  }
}
```

### List conversations on a channel
```http
GET /sms/conversations/{channel_uuid}
```
Lists the distinct contacts that have exchanged SMS with this channel, each with its latest message, ordered newest-first.

Query params: `page_number` (optional) — zero-based page index (default `0`), `results_per_page` (optional) — page size 1–200 (default `20`).

**Success response (HTTP 200):**
```json
{
  "success": true,
  "conversations": [
    {
      "contact": "+442079460958",
      "last_message_text": "Thanks, got it!",
      "last_message_direction": "inbound",
      "last_message_ts": 1771502400,
      "last_message_at": "2026-02-19T14:00:00Z",
      "message_count": 7
    }
  ],
  "count": 1,
  "page": 0
}
```
`count` is the total number of distinct contacts (conversations) on the channel.

### Read a conversation thread
```http
GET /sms/messages/{channel_uuid}/{contact_number}
```
Returns the full SMS thread between the channel and a contact, inbound and outbound merged and sorted oldest → newest. `contact_number` is the contact in E.164 format.

Query params: `results_per_page` (optional) — page size 1–200 (default `50`), `before_ts` (optional) — unix-seconds cursor; only messages older than this are returned (page back in time).

**Success response (HTTP 200):**
```json
{
  "success": true,
  "messages": [
    {
      "uuid": "SMIb3d4e5f6-7a8b-4c9d-0e1f-2a3b4c5d6e7f",
      "destination": "+17135550182",
      "source": "+442079460958",
      "text": "Hi, is my order ready?",
      "direction": "inbound",
      "price": 0.0,
      "voip_number_setup_id": 4821,
      "created_at": "2026-02-19T13:58:30Z",
      "updated_at": "2026-02-19T13:58:30Z",
      "timestamp": 1771502310
    },
    {
      "uuid": "SMOc4e5f6a7-8b9c-4d0e-1f2a-3b4c5d6e7f80",
      "destination": "+442079460958",
      "source": "+17135550182",
      "text": "Yes! It ships today.",
      "fragments": 1,
      "direction": "outbound",
      "price": 0.005,
      "voip_number_setup_id": 4821,
      "created_at": "2026-02-19T13:59:10Z",
      "updated_at": "2026-02-19T13:59:10Z",
      "timestamp": 1771502350
    }
  ],
  "count": 2
}
```
Each message carries a `direction` (`inbound` / `outbound`) and a unix `timestamp`. Inbound (`SMI`) messages have no `fragments` field; outbound (`SMO`) messages include `fragments` and a customer-facing `price`.

### Get a single message
```http
GET /sms/message/{message_uuid}
```
Returns one SMS message by its UUID. `message_uuid` must start with `SMI` (inbound) or `SMO` (outbound).

**Success response (HTTP 200) — outbound example:**
```json
{
  "success": true,
  "message": {
    "uuid": "SMOc4e5f6a7-8b9c-4d0e-1f2a-3b4c5d6e7f80",
    "destination": "+442079460958",
    "source": "+17135550182",
    "text": "Yes! It ships today.",
    "fragments": 1,
    "direction": "outbound",
    "price": 0.005,
    "voip_number_setup_id": 4821,
    "created_at": "2026-02-19T13:59:10Z",
    "updated_at": "2026-02-19T13:59:10Z"
  }
}
```

**Error response (read endpoints):**
```json
{
  "error": true,
  "error_message": "Description of what went wrong"
}
```
