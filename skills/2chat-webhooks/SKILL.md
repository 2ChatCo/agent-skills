---
name: 2chat-webhooks
description: Use this skill when the user wants to subscribe to, list, or delete webhook subscriptions in 2Chat — for WhatsApp messages, calls, groups, or phone call events. Trigger phrases include "subscribe webhook", "create webhook", "list webhooks", "delete webhook", "listen for messages", "real-time events", "webhook events".
---

# 2Chat — Webhooks

Subscribe to real-time events across WhatsApp and Phone Calls via the [2Chat API](https://developers.2chat.co/).

## Authentication

All requests require the header:

```
X-User-API-Key: <api_key>
```

API keys are generated at [app.2chat.io/developers](https://app.2chat.io/developers). Base URL: `https://api.p.2chat.io/open`

---

## Subscribe to an event
```http
POST /webhooks/subscribe/{event_name}
```

**For WhatsApp events:**
```json
{
  "hook_url": "https://your-server.com/webhook",
  "on_number": "+15557654321"
}
```
Optional fields:
- `to_group_uuid` — restrict group events to a specific group (default: `any`)
- `time_period` — required for `whatsapp.conversation.new` (`all-time`, `hour`, `day`, `week`, `month`, `year`)

**For Phone Call events:**
```json
{
  "hook_url": "https://your-server.com/webhook",
  "channel_uuid": "VN-uuid-of-virtual-number"
}
```

Returns: webhook `uuid`, `event_name`, `channel_uuid`, `hook_url`, `created_at`.

---

## WhatsApp Events

### Messages
| Event | Description |
|---|---|
| `whatsapp.message.new` | Any new message (sent or received) |
| `whatsapp.message.received` | Inbound person-to-person message |
| `whatsapp.message.sent` | Outbound message from agent |
| `whatsapp.message.reaction` | Emoji reaction on a message |
| `whatsapp.message.edited` | Message was edited (includes old and new text) |

### Receipts
| Event | Description |
|---|---|
| `whatsapp.message.receipt.sent` | Message sent to WhatsApp network |
| `whatsapp.message.receipt.received` | Message received by recipient |
| `whatsapp.message.receipt.read` | Message read by recipient |

### Conversations
| Event | Description |
|---|---|
| `whatsapp.conversation.new` | New conversation started (requires `time_period`) |

### Calls
| Event | Description |
|---|---|
| `whatsapp.call.received` | Incoming WhatsApp call |
| `whatsapp.call.made` | Outgoing WhatsApp call |

### Groups
| Event | Description |
|---|---|
| `whatsapp.group.message.received` | New message in a group |
| `whatsapp.group.message.reaction` | Reaction on a group message |
| `whatsapp.group.join` | Someone joined a group |
| `whatsapp.group.leave` | Someone left a group |
| `whatsapp.group.remove` | Someone was removed from a group |

### Orders
| Event | Description |
|---|---|
| `whatsapp.order.received` | WhatsApp Commerce order received |

### Audio
| Event | Description |
|---|---|
| `whatsapp.audio.transcribed` | Audio message transcribed |

### Status
| Event | Description |
|---|---|
| `whatsapp.number.status` | Number connection status changed (QR received, disconnected, etc.) |

---

## WABA Events

| Event | Description |
|---|---|
| `whatsapp.waba.message.sent` | WABA message sent |
| `whatsapp.waba.message.receipt.sent` | WABA message reached WhatsApp network |
| `whatsapp.waba.message.receipt.received` | WABA message received by recipient |

---

## Phone Call Events

| Event | Description |
|---|---|
| `call.status.update` | Call status changed (ringing, answered, ended) |
| `call.incoming.completed` | Incoming call completed (includes agent info) |
| `call.outbound.completed` | Outbound call completed (includes cost info) |

---

## List all webhooks
```http
GET /webhooks
```
Returns all webhook subscriptions with `uuid`, `event_name`, `channel_uuid`, `hook_url`, `hook_params`, `created_at`.

## List webhooks by channel
```http
GET /webhooks/channel/{channel_uuid}
```
Returns webhooks filtered by a specific channel (WhatsApp number or virtual phone number).

## Delete a webhook
```http
DELETE /webhooks/{webhook_uuid}
```
Get webhook UUIDs from the list endpoints.
