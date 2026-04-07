---
name: 2chat-whatsapp-groups
description: Use this skill when the user wants to create or manage WhatsApp groups via the 2Chat API — list groups, add or remove participants, promote or demote admins, set group description or picture. Trigger phrases include "create WhatsApp group", "add to group", "remove from group", "list groups", "group participants", "promote admin", "set group picture".
---

# 2Chat — WhatsApp Groups

Create and manage WhatsApp groups via the [2Chat API](https://developers.2chat.co/).

## Authentication

All requests require the header:

```
X-User-API-Key: <api_key>
```

API keys are generated at [app.2chat.io/developers](https://app.2chat.io/developers). Base URL: `https://api.p.2chat.io/open`

---

## List groups
```http
GET /whatsapp/groups/{phone_number}
```
Returns array of groups with `uuid`, `wa_group_name`, `size`, `is_muted`, `is_read_only`, `channel_is_owner`, `profile_pic_url`, and `owner_contact`.

## Create group
```http
POST /whatsapp/group/create
```
```json
{
  "from_number": "+595981048477",
  "group": {
    "name": "My cool WhatsApp group",
    "description": "Group description",
    "participants": ["+17137157533", "+18647351567"]
  }
}
```
Max 10 initial participants. Response includes `uuid`, `accepted_numbers`, and `unaccepted_numbers`. Use Add Participant for additional members.

WhatsApp has a non-published soft limit on group creation rate.

## Get group details and participants
```http
GET /whatsapp/group/{group_uuid}
```
Returns group metadata and full participant list. Each participant includes `wa_is_admin`, `wa_is_super_admin`, `wa_pushname`, `phone_number`, `device`, and linked `contact`.

## Add participants
```http
POST /whatsapp/group/{group_uuid}/add-participant
```
```json
{
  "from_number": "+12123334444",
  "participants": ["+447700176576", "+17137157533"]
}
```
Max 10 per request. Response shows per-number status and `unaccepted_numbers`.

## Remove participants
```http
POST /whatsapp/group/{group_uuid}/remove-participant
```
```json
{
  "from_number": "+12123334444",
  "participants": ["+447700176576", "+17137157533"]
}
```
Max 10 per request. Returns `accepted_numbers` and `unaccepted_numbers`.

## Promote to admin
```http
POST /whatsapp/group/{group_uuid}/promote-participant
```
```json
{
  "from_number": "+12123334444",
  "participants": ["+447700176576"]
}
```
Max 10 per request. Changes may take a few minutes to reflect.

## Demote from admin
```http
POST /whatsapp/group/{group_uuid}/demote-participant
```
```json
{
  "from_number": "+12123334444",
  "participants": ["+447700176576"]
}
```
Max 10 per request.

## Set group description
```http
POST /whatsapp/group/{group_uuid}/set-description
```
```json
{
  "from_number": "+12123334444",
  "description": "New group description"
}
```

## Set group picture
```http
POST /whatsapp/group/{group_uuid}/set-picture
```
```json
{
  "from_number": "+12123334444",
  "url": "https://example.com/group-photo.jpg"
}
```
Image URL must be publicly accessible.
