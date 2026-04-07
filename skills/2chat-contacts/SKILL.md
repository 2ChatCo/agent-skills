---
name: 2chat-contacts
description: Use this skill when the user wants to create, list, search, update, or delete contacts in 2Chat. Trigger phrases include "create contact", "list contacts", "search contacts", "find contact", "update contact", "delete contact", "2chat contacts".
---

# 2Chat — Contacts

Manage your contact directory via the [2Chat API](https://developers.2chat.co/).

## Authentication

All requests require the header:

```
X-User-API-Key: <api_key>
```

API keys are generated at [app.2chat.io/developers](https://app.2chat.io/developers). Base URL: `https://api.p.2chat.io/open`

---

## List contacts
```http
GET /contacts
```
Query params: `page_number`, `results_per_page` (1-100), `channel_uuid` (filter by connected number).

Returns contacts with `uuid`, `first_name`, `last_name`, `channel_uuid`, `profile_pic_url`, and `details` (phone numbers, emails).

## Get a contact
```http
GET /contacts/{contact_uuid}
```
Returns contact details by UUID. Obtain UUIDs via list or search.

## Search contacts
```http
GET /contacts/search?q={phone_or_name}
```
Query params: `q` (required — phone number or name), `channel_uuid` (filter by number), `page_number`, `results_per_page` (1-100).

Returns matching contacts with linked details.

## Create a contact
```http
POST /contacts
```
```json
{
  "first_name": "John",
  "last_name": "Doe",
  "profile_pic_url": "https://example.com/photo.jpg",
  "channel_uuid": "WPN-uuid",
  "contact_detail": [
    { "type": "WAPH", "value": "+15551234567" },
    { "type": "E", "value": "john@example.com" }
  ]
}
```

Detail types:
| Type | Description |
|---|---|
| `PH` | Phone number |
| `WAPH` | WhatsApp phone number |
| `E` | Email |
| `A` | Address |

Setting `channel_uuid` syncs the contact to that WhatsApp number.

## Update a contact
```http
PUT /contacts/{contact_uuid}
```
```json
{
  "first_name": "John",
  "last_name": "Smith",
  "profile_pic_url": "https://example.com/new-photo.jpg",
  "channel_uuid": "WPN-uuid",
  "contact_details": {}
}
```
Changing `channel_uuid` syncs the contact to the new WhatsApp number.

## Delete a contact
```http
DELETE /contacts/{contact_uuid}
```
