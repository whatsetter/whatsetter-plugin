# WhatSetter plugin

Connect your agent to your [WhatSetter](https://whatsetter.com) workspace. WhatSetter is an AI appointment setter on WhatsApp: it contacts your leads, qualifies them in conversation and books meetings on your calendar. With this plugin, your agent works the account in plain language: morning briefing, inbox triage, lead import, bookings report, campaign control, CRM sync.

Works with **Grok Bot**, **Cursor**, the **Grok** CLI and **Claude Code**.

## Install

### Grok Bot and Cursor

Open the **Plugins** tab (Grok Bot → Plugins, or Cursor Settings → Plugins), search **WhatSetter** and click **Add**. In Cursor chat you can also run `/add-plugin whatsetter`.

### Grok CLI

```
grok plugin marketplace add whatsetter/whatsetter-plugin
grok plugin install whatsetter --trust
```

### Claude Code

```
/plugin marketplace add whatsetter/whatsetter-plugin
/plugin install whatsetter@whatsetter
```

### Any other MCP client

Point it at `https://mcp.whatsetter.com/mcp` (Streamable HTTP). OAuth is discovered automatically. For server-to-server use, send the header `Authorization: Bearer ws_live_…` instead.

## Connect your account

1. In WhatSetter, open **Settings → API** and create a key. Give it only the scopes you need (see below).
2. The first time your agent calls a WhatSetter tool, your client opens the **Connect to WhatSetter** page. Paste the key and approve.
3. That's it. The key is checked live, stored encrypted on the WhatSetter server, and your client only holds a revocable token. Revoking the key in the dashboard cuts access immediately.

There is no API key to put in the plugin configuration, and nothing is stored in this repository.

| Scope | Lets the agent |
|---|---|
| `leads:read` / `leads:write` | Read leads / update their status and tags |
| `conversations:read` | Read the inbox and message history |
| `lists:read` / `lists:write` | List and create contact lists, import contacts |
| `campaigns:read` / `campaigns:write` | List campaigns / pause and resume them |
| `messages:send` | Send WhatsApp messages (anti-ban enforced) |
| `bookings:read` | Read booked meetings |
| `groups:read` | Read managed WhatsApp groups: members, messages, joins and leaves |
| `webhooks:manage` | Create, delete and test webhooks |

A good first key for a daily briefing: `leads:read`, `conversations:read`, `campaigns:read`, `bookings:read`. Add `messages:send` only if you want the agent to reply on your behalf, with your approval on every message.

## What the agent can do

| Domain | Tools |
|---|---|
| Identity | `whoami` |
| Leads | `list_leads`, `get_lead`, `update_lead` |
| Conversations | `list_conversations`, `get_conversation_messages` |
| Messaging | `send_whatsapp_message` |
| Lists | `list_lists`, `create_list`, `import_leads` |
| Campaigns | `list_campaigns`, `pause_campaign`, `resume_campaign` |
| Bookings | `list_bookings` |
| Groups | `list_groups`, `get_group`, `list_group_members`, `list_group_messages`, `list_group_events` |
| Webhooks | `list_webhooks`, `create_webhook`, `delete_webhook`, `test_webhook` |

The bundled `whatsetter` skill teaches the agent how to use them together and when to stop and ask.

## Commands

In the Grok CLI and Claude Code these are slash commands (`/whatsetter:briefing`). In Grok Bot and Cursor, just ask for the same thing in plain language.

| Command | What it does |
|---|---|
| `/whatsetter:briefing` | Read-only briefing: new replies, leads waiting on us, meetings booked, campaign and number health |
| `/whatsetter:inbox` | Triage the inbox and draft replies, sent only with your approval |
| `/whatsetter:import` | Import contacts into a list after a preview |
| `/whatsetter:bookings` | Meetings booked by the setter, per campaign |
| `/whatsetter:campaign` | Show, pause or resume a campaign by name |

## Safety

- **Nothing sends without your yes.** `send_whatsapp_message` is the only tool that reaches a real person. The skill makes the agent show you the exact recipient and text and wait for your confirmation. Every tool carries `readOnlyHint` / `destructiveHint` annotations, so clients can auto-approve reads and always prompt on writes.
- **Anti-ban is enforced by the WhatSetter API, not by a prompt.** A new number can't be messaged cold: first contact always goes through a list and a campaign. Each WhatsApp number has a shared daily message budget. Every send replays a human rhythm (read receipt, typing, paced send) and carries an idempotency key, so a retry can never double-send.
- **The AI setter keeps working.** A message sent through the plugin does not pause the setter: it keeps answering the lead afterwards and sees your message in the history.

## Example prompts

- *"What happened in my WhatsApp inbox since yesterday? Draft replies for the leads waiting on us, don't send anything."*
- *"Import this CSV into a new list called September webinar."*
- *"How many meetings did the setter book this week, per campaign?"*
- *"Pause my Dubai campaign."*
- *"Every time a lead is qualified, send it to https://hooks.example.com/crm."*
- *"Who joined and who left my webinar group this week?"*

## Network endpoints and credentials

This plugin ships no code, hooks or scripts: only markdown and one MCP server URL.

| Endpoint | Purpose |
|---|---|
| `https://mcp.whatsetter.com/mcp` | Hosted WhatSetter MCP server (Streamable HTTP) |
| `https://mcp.whatsetter.com/authorize`, `/token`, `/register` | OAuth 2.1 with PKCE and dynamic client registration |

Credential: a WhatSetter API key (`ws_live_…`), entered on the WhatSetter consent page, never in this repository or in your client configuration.

## Requirements

A WhatSetter workspace at [app.whatsetter.com](https://app.whatsetter.com). Sending messages and running campaigns need at least one connected WhatsApp number.

## Links

- Documentation: [docs.whatsetter.com/developers/mcp](https://docs.whatsetter.com/developers/mcp/)
- API keys and scopes: [docs.whatsetter.com/developers/authentication](https://docs.whatsetter.com/developers/authentication/)
- Anti-ban protections: [docs.whatsetter.com/dashboard/anti-ban](https://docs.whatsetter.com/dashboard/anti-ban/)
- Support: [contact@whatsetter.com](mailto:contact@whatsetter.com)

## License

MIT
