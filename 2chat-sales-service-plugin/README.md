# 2chat-sales-service

A Claude plugin with skills to support **sales**, **customer service**, and **WhatsApp group management** processes for businesses, using the [2Chat](https://2chat.co/) MCP connector.

## Requirements

You'll need a [2Chat](https://2chat.co/) account

- The **2Chat MCP** connector must be enabled on your Claude account (under Settings → Connectors), using that API key. All skills in this plugin depend on its tools (WhatsApp Web messaging, WABA, groups, contacts, catalog).

## Installation

### Option A — Claude Desktop / Cowork (.plugin file)

1. Download the packaged `2chat-sales-service-plugin.plugin` file from this [link](https://github.com/2ChatCo/agent-skills/blob/main/2chat-sales-service-plugin/2chat-sales-service.plugin).
2. Open Claude Desktop/Cowork go to **Customize**.
3. Select **Plugins**
4. Click on the button **Add** and select **Upload Plugin**


### Option B — Claude Code (marketplace)

```bash
# Add this repo as a plugin marketplace
claude plugin marketplace add 2ChatCo/agent-skills/2chat-sales-service-plugin

# Install the plugin
claude plugin install 2chat-sales-service-plugin@2chat-sales-service-plugin
```

> Replace `2ChatCo/agent-skills` with the GitHub username or org this repo is hosted under.

### Option C — Claude Code (local clone)

```bash
git clone https://github.com/2ChatCo/agent-skills/2chat-sales-service-plugin.git
cd 2chat-sales-service-plugin

# Load it for a single session
claude --plugin-dir .

# Or install it persistently from the local path
claude plugin install 2chat-sales-service-plugin@local --plugin-dir .
```

### Building the `.plugin` file yourself

From the root of this repo:

```bash
zip -r 2chat-sales-service-plugin.plugin . -x "*.DS_Store" -x ".git/*"
```

## Included skills

### Sales
- **whatsapp-lead-qualifier** — Detects and scores inbound leads from WhatsApp conversations, and saves qualification notes on the 2Chat contact.
- **whatsapp-catalog-pitch** — Recommends products from the WhatsApp Business catalog and drafts the sales message (free-form message or WABA template depending on the 24-hour window).
- **whatsapp-followup-sequencer** — Detects leads that have gone cold and drafts/sends a contextual follow-up message.

### Customer service
- **whatsapp-unanswered-triage** — Finds unanswered WhatsApp messages, classifies them (urgent, complaint, billing, lead, support, other), and helps draft/send replies.
- **whatsapp-sla-monitor** — Measures response times per conversation and reports SLA compliance.
- **whatsapp-template-manager** — Creates, reviews, cost-estimates, and syncs WABA templates.
- **whatsapp-conversation-summarizer** — Summarizes a long conversation thread into an executive summary with next steps, useful for handoffs.

### Group management
- **whatsapp-group-health-report** — Reports on the size and activity level of WhatsApp groups, flagging inactive ones.
- **whatsapp-group-broadcaster** — Drafts and sends announcements/promotions to one or more groups, with basic frequency checks.
- **whatsapp-group-moderator-digest** — Periodic digest of what was discussed in a group, highlighting unanswered questions.

## Usage

Simply ask in natural language for what you need — for example:
- "Check unanswered whatsapp messages from the last 7 days"
- "Qualify the leads that came in this week over whatsapp"
- "How's our whatsapp response SLA looking this month?"
- "Summarize today's activity in the reseller group"

Each skill triggers automatically based on the phrasing used; there's no need to invoke them by name.

## Safety notes

- No skill sends messages, templates, or broadcasts without asking for explicit confirmation before each send.
- Read-only skills (reports, summaries, qualification) don't require confirmation to query data, but do require it before writing to contacts or sending messages.
