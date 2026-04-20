---
name: 2chat-numbers
description: Use this skill when the user wants to search for phone numbers, check number availability, view regions/cities/DID types, check regulatory requirements, reserve numbers, cancel reservations, purchase phone numbers, or delete/cancel an owned virtual number via the 2Chat API. Trigger phrases include "search numbers", "buy number", "purchase number", "reserve number", "delete number", "cancel number", "phone number", "DID", "number groups", "available numbers", "2chat numbers".
---

# 2Chat — Numbers

Search, reserve, and purchase phone numbers via the [2Chat API](https://developers.2chat.co/).

## Authentication

All requests require the header:

```
X-User-API-Key: <api_key>
```

API keys are generated at [app.2chat.io/developers](https://app.2chat.io/developers). Base URL: `https://api.p.2chat.io/open`

---

## Purchase flow

The typical flow is: **Pick a location** -> **Search for number groups** -> **Check requirements** -> **Purchase** (optionally reserving first).

---

## Get Regions
```http
GET /numbers/regions?country={ISO_CODE}
```
Query params: `country` (required) — ISO country code (e.g., US, CA, GB).

Returns regions available for the given country. Regions are available for US, CA, and GB. Required for US searches.

Response:
```json
{
  "success": true,
  "data": {
    "regions": [
      { "id": 123, "region_name": "California", "iso_country_code": "US" }
    ]
  }
}
```

## Get Cities
```http
GET /numbers/cities?country={ISO_CODE}&region={REGION_ID}
```
Query params: `country` (required) — ISO country code, `region` (optional) — region ID from the regions endpoint. If omitted, returns all cities for the country.

Response:
```json
{
  "success": true,
  "data": {
    "cities": [
      { "id": 456, "city_name": "Los Angeles", "iso_country_code": "US" }
    ]
  }
}
```

## Get DID Types
```http
GET /numbers/did-types
```
No query params. Returns all available number types.

Valid types: `local`, `mobile`, `tollfree`, `national`, `global`, `sharedcost`.

Response:
```json
{
  "success": true,
  "data": {
    "did_types": [
      { "id": 1, "name": "local" },
      { "id": 2, "name": "mobile" }
    ]
  }
}
```

## Get Requirements
```http
GET /numbers/requirements?country={ISO_CODE}&did_type={DID_TYPE_ID}
```
Query params: `country` (required) — ISO country code, `did_type` (optional) — DID type ID to filter requirements.

Some countries require identity documents or proof of address. Check requirements before purchasing — buying without meeting them can result in the number being suspended. If the array is empty, no documents are needed.

Response:
```json
{
  "success": true,
  "data": {
    "requirements": [
      { "description": "Proof of address required", "did_type": "local" }
    ]
  }
}
```

## Search Number Groups
```http
GET /numbers/groups?country={ISO_CODE}
```
This is the main search endpoint. Query params: `country` (required), `region_id` (optional), `city_id` (optional), `city_name_or_prefix` (optional) — search by city name or area code prefix, `did_type` (optional) — `local`, `mobile`, `tollfree`, `national`, `global`, `sharedcost`, or `all`.

Response:
```json
{
  "success": true,
  "data": {
    "groups": [
      {
        "id": "abc-123",
        "prefix": "+1 (213)",
        "location": "Los Angeles",
        "price": {
          "setup_price": "5.00",
          "monthly_price": "3.00",
          "ppm": "0.02"
        },
        "type": "local",
        "requirements": [],
        "allow_number_selection": true,
        "is_eligible_for_free_number": false,
        "features": ["voice", "sms_in", "sms_out", "whatsapp_business"]
      }
    ]
  }
}
```

Key fields:
- `allow_number_selection` — if `true`, use the Available Numbers endpoint to browse specific numbers in this group
- `features` — can include `voice`, `sms_in`, `sms_out`, `whatsapp` (mobile only), `whatsapp_business`
- `requirements` — if non-empty, documents are needed before purchase

## Get Available Numbers
```http
GET /numbers/available?group_id={GROUP_ID}&number_contains={DIGITS}
```
Query params: `group_id` (required) — group ID from the search results, `number_contains` (optional) — search for vanity numbers containing specific digits.

Only works for groups where `allow_number_selection` is `true`.

Response:
```json
{
  "success": true,
  "data": {
    "numbers": [
      { "id": "did-789", "number": "+12135551234" }
    ]
  }
}
```

## Reserve a Number
```http
POST /numbers/reservations
```
```json
{
  "group_id": "abc-123",
  "number": "+12135551234"
}
```
`group_id` (required) — the group to reserve from. `number` (optional) — specific number to reserve; if omitted, a random number from the group is reserved.

Reservations expire. Always purchase before the `expires_at` time.

Response (HTTP 201):
```json
{
  "success": true,
  "data": {
    "reservation_details": {
      "uuid": "res-uuid-123",
      "number": "+12135551234",
      "created_at": "2024-01-15T10:00:00Z",
      "expires_at": "2024-01-15T10:30:00Z"
    }
  }
}
```

## List Reservations
```http
GET /numbers/reservations
```
Returns all active reservations for the account.

Response:
```json
{
  "success": true,
  "data": {
    "reservations": [
      {
        "uuid": "res-uuid-123",
        "number": "+12135551234",
        "did_group_id": "abc-123",
        "did_type": "local",
        "iso_country_code": "US",
        "location": "Los Angeles",
        "setup_price": 5.00,
        "monthly_price": 3.00,
        "ppm": 0.02,
        "reservation_date": "2024-01-15T10:00:00Z",
        "reservation_end_date": "2024-01-15T10:30:00Z",
        "needs_registration": false,
        "supports_sms_in": true,
        "supports_sms_out": true
      }
    ]
  }
}
```

## Cancel a Reservation
```http
DELETE /numbers/reservations/{uuid}
```
Path params: `uuid` (required) — the reservation UUID.

Response:
```json
{
  "success": true,
  "message": "Reservation res-uuid-123 cancelled successfully"
}
```

## Purchase Numbers
```http
POST /numbers/purchase
```
```json
{
  "number": "+12135551234"
}
```
`number` (optional) — specific number to purchase. If the number was previously reserved, the reservation is used. If not reserved, availability is checked on the fly. If omitted, all active reservations are purchased.

Response (HTTP 201):
```json
{
  "success": true,
  "purchased": ["+12135551234"],
  "failed": []
}
```

## Delete a Virtual Number
```http
DELETE /voip/virtual-numbers/{virtual_number_uuid}
```
Path params: `virtual_number_uuid` (required) — the virtual number UUID, formatted as `DID` followed by a 36-character UUID (e.g., `DID01234567-89ab-cdef-0123-456789abcdef`).

Marks an owned virtual number as pending cancellation. Use this to release a number you no longer want. Note the full path is `/open/voip/virtual-numbers/...` — this lives under the VoIP namespace, not `/numbers/`.

Response:
```json
{
  "success": true
}
```

## Quick example: Buy a local number in Los Angeles

```bash
# 1. Find the California region
curl 'https://api.p.2chat.io/open/numbers/regions?country=US' \
  -H 'X-User-API-Key: YOUR_KEY'

# 2. Find Los Angeles
curl 'https://api.p.2chat.io/open/numbers/cities?country=US&region=123' \
  -H 'X-User-API-Key: YOUR_KEY'

# 3. Search for local number groups in LA
curl 'https://api.p.2chat.io/open/numbers/groups?country=US&city_id=456&did_type=local' \
  -H 'X-User-API-Key: YOUR_KEY'

# 4. Browse available numbers in that group
curl 'https://api.p.2chat.io/open/numbers/available?group_id=abc-123' \
  -H 'X-User-API-Key: YOUR_KEY'

# 5. Purchase the number
curl -X POST 'https://api.p.2chat.io/open/numbers/purchase' \
  -H 'X-User-API-Key: YOUR_KEY' \
  -H 'Content-Type: application/json' \
  -d '{"number": "+12135551234"}'
```

## Error responses

All endpoints return errors in this format:
```json
{
  "success": false,
  "error": true,
  "error_message": "Description of what went wrong"
}
```
