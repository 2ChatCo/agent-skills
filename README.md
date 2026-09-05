# 2Chat Agent Skills

A collection of agent skills that enable AI agents to interact with the [2Chat API](https://developers.2chat.co/) — send and receive WhatsApp messages, manage contacts, groups, catalogs, webhooks, phone calls, and SMS.

## Plugins
* **[2chat-sales-service](https://github.com/2ChatCo/agent-skills/tree/main/2chat-sales-service-plugin)** A Claude plugin with skills to support sales, customer service, and WhatsApp group management processes for businesses, using the 2Chat MCP connector.

## Skills

| Skill | Description |
|---|---|
| **2chat-whatsapp** | Send/receive WhatsApp messages, manage numbers, conversations, and profile |
| **2chat-whatsapp-groups** | Create and manage WhatsApp groups, participants, and settings |
| **2chat-whatsapp-catalog** | Manage WhatsApp Business product catalogs and collections |
| **2chat-whatsapp-waba** | Send template and session messages via WhatsApp Business API (WABA) |
| **2chat-contacts** | Create, list, search, update, and delete contacts |
| **2chat-webhooks** | Subscribe to real-time events for WhatsApp, WABA, and Phone Calls |
| **2chat-calls** | View call history, details, recordings, and manage virtual numbers |
| **2chat-sms** | Send SMS messages |

## Requirements

You'll need a [2Chat](https://2chat.co) account and an API key generated at [app.2chat.io/developers](https://app.2chat.io/developers).

## Skill Structure

```
skills/
  2chat-whatsapp/SKILL.md
  2chat-whatsapp-groups/SKILL.md
  2chat-whatsapp-catalog/SKILL.md
  2chat-whatsapp-waba/SKILL.md
  2chat-contacts/SKILL.md
  2chat-webhooks/SKILL.md
  2chat-calls/SKILL.md
  2chat-sms/SKILL.md
```

## Installation

Install all skills:
```
npx skills add 2ChatCo/agent-skills
```

Install a specific skill:
```
npx skills add 2ChatCo/agent-skills -s 2chat-whatsapp
```

Or add manually by copying the desired skill folders into your agent's skills directory.

After installing, just mention what you want to do. For example:
- "Send a WhatsApp message to +1555123456 saying hello"
- "Check if +1555123456 is on WhatsApp"
- "List my recent WhatsApp conversations"
- "Add a product to my WhatsApp catalog"
- "Send a WABA template message"
- "Show my call history"

## Updating

Skills can be updated with:
```
npx skills update
```

You can also check for available updates with `npx skills check`.

## Learn More

- [2Chat API Documentation](https://developers.2chat.co/)
- [2Chat Website](https://2chat.co)
