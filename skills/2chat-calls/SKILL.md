---
name: 2chat-calls
description: Use this skill when the user wants to view phone call history, get call details or recordings, or list virtual numbers and caller IDs via the 2Chat API. Trigger phrases include "call history", "call details", "call recording", "list virtual numbers", "caller IDs", "phone calls", "2chat calls".
---

# 2Chat — Phone Calls

View call history, details, recordings, and manage virtual numbers and caller IDs via the [2Chat API](https://developers.2chat.co/).

## Authentication

All requests require the header:

```
X-User-API-Key: <api_key>
```

API keys are generated at [app.2chat.io/developers](https://app.2chat.io/developers). Base URL: `https://api.p.2chat.io/open`

---

## Call History

### Get call history
```http
GET /voip/call-history
```
Query params: `page_number`, `direction` (`inbound` or `outbound`).

Returns call detail records (CDRs) with `uuid`, `direction` (`O`=outbound, `I`=inbound), `status`, `to_number`, `caller_id_used`, `duration` (seconds), `recording_url`, `start_time`, `end_time`, `agent_id`, and `call_cost` (with `call_price` in USD).

### Get call details
```http
GET /voip/call/{call_uuid}
```
Returns a single CDR with full details including `agent` info (name, profile pic) and `call_cost`.

---

## Virtual Numbers

### List virtual numbers
```http
GET /voip/virtual-numbers
```
Query params: `page_number`, `results_per_page` (default 50).

Returns numbers with `uuid`, `phone_number`, `iso_country_code`, `friendly_name`, `record_inbound`, `is_toll_free`, `incoming_price_per_minute`, `status_text` (`ACTIVE`, `AWAITING_REGISTRATION`, `PENDING_ACTIVATION`, `PENDING_CANCELLATION`, `RELEASED`).

### List caller IDs
```http
GET /voip/caller-ids
```
Query params: `page_number`, `results_per_page` (default 50).

Returns caller IDs with `uuid`, `phone_number`, `friendly_name`, `type`, `verified`.

Caller ID types:
| Type | Description |
|---|---|
| `VN` | Virtual number purchased on 2Chat |
| `WW` | Connected WhatsApp number |
| `WA` | WhatsApp Business API number |
| `CI` | Imported custom caller ID |
