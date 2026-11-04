# WhatSetter plugin

Connect your agent to your [WhatSetter](https://whatsetter.com) workspace. WhatSetter runs AI agents that talk to your leads on **WhatsApp, Instagram and Messenger**: they answer, qualify and book meetings on your calendar. It also plans your Instagram posts and runs comment and private-message automations. With this plugin, your agent works the account in plain language: morning briefing, inbox triage across channels, why an agent said something and how to fix it, lead import, bookings, Instagram results and planning, CRM sync.

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
3. That's it. The key is checked live and kept on the WhatSetter server, never in your client: your client only holds a revocable token. Revoking the key in the dashboard cuts access within a minute.

There is no API key to put in the plugin configuration, and nothing is stored in this repository. A key's scopes can't be changed later: to add one, create a new key and reconnect with it.

| Scope | Lets the agent |
|---|---|
| `leads:read` / `leads:write` | Read leads and their notes / update status, tags, owner, next action and outcome, add notes, hand a conversation to a person and give it back |
| `conversations:read` | Read the inbox and message history on every channel |
| `messages:send` | Send messages on WhatsApp, Instagram and Messenger, and push one opt-in contact to a WhatsApp agent |
| `lists:read` / `lists:write` | List and create contact lists, import contacts |
| `campaigns:read` / `campaigns:write` | List agents / pause and resume them |
| `prompts:read` / `prompts:write` | Read an agent's prompts and their versions / change or restore them |
| `knowledge:read` / `knowledge:write` | Read and search knowledge bases / add, correct and remove answers and text files |
| `instagram:read` / `instagram:write` | Read accounts, post results, best times, planned posts and automations / publish or schedule posts, manage automations |
| `bookings:read` | Read booked meetings |
| `groups:read` | Read managed WhatsApp groups: members, messages, joins and leaves |
| `webhooks:manage` | Create, delete and test webhooks |
| `blocklist:manage` | Block or unblock a number or a country code |
| `team:read` | List the workspace's members |
| `links:read` | Read tracked WhatsApp links and their clicks |

A good first key for a daily briefing: `leads:read`, `conversations:read`, `campaigns:read`, `bookings:read`, and `instagram:read` if you use Instagram. Add `messages:send` only if you want the agent to reply on your behalf, with your approval on every message.

Your plan decides what the API opens: the Social plan (Instagram and Messenger) has no WhatsApp sending, contact lists, groups, tracked links or webhooks. Instagram and Messenger tools need the channel connected in the dashboard.

## What the agent can do

66 tools on the hosted WhatSetter MCP server:

| Domain | Tools |
|---|---|
| Identity | `whoami` |
| Leads | `list_leads`, `get_lead`, `update_lead`, `handoff_lead`, `resume_lead`, `list_lead_notes`, `add_lead_note` |
| Conversations | `list_conversations`, `get_conversation_messages` |
| Messaging | `send_message` (also as `send_whatsapp_message`, its first name) |
| Lists | `list_lists`, `create_list`, `import_leads` |
| Agents | `list_campaigns`, `pause_campaign`, `resume_campaign`, `add_campaign_lead` |
| Prompts | `get_agent_prompts`, `get_agent_prompt`, `update_agent_prompt`, `list_prompt_versions`, `get_prompt_version`, `compare_prompt_versions`, `restore_prompt_version` |
| Knowledge | `list_knowledge_bases`, `get_knowledge_base`, `search_knowledge`, `list_knowledge_answers`, `add_knowledge_answer`, `update_knowledge_answer`, `delete_knowledge_answer`, `get_knowledge_file`, `add_knowledge_file`, `update_knowledge_file`, `delete_knowledge_file` |
| Instagram | `list_instagram_accounts`, `list_instagram_posts`, `get_instagram_best_times`, `get_instagram_stats`, `list_scheduled_instagram_posts`, `get_scheduled_instagram_post`, `schedule_instagram_post`, `update_scheduled_instagram_post`, `cancel_scheduled_instagram_post`, `list_instagram_automations`, `create_instagram_automation`, `update_instagram_automation`, `delete_instagram_automation` |
| Bookings | `list_bookings` |
| Groups | `list_groups`, `get_group`, `list_group_members`, `list_group_messages`, `list_group_events` |
| Webhooks | `list_webhooks`, `create_webhook`, `delete_webhook`, `test_webhook` |
| Blocklist | `list_blocked_contacts`, `block_contact`, `unblock_contact` |
| Team | `list_team_members` |
| Tracked links | `list_links`, `get_link` |

The bundled `whatsetter` skill teaches the agent how to use them together and when to stop and ask.

## Commands

In the Grok CLI and Claude Code these are slash commands (`/whatsetter:briefing`). In Grok Bot and Cursor, just ask for the same thing in plain language.

| Command | What it does |
|---|---|
| `/whatsetter:briefing` | Read-only briefing across channels: new replies, leads waiting on us, meetings booked, agent and account health |
| `/whatsetter:inbox` | Triage the inbox (WhatsApp, Instagram, Messenger) and draft replies, sent only with your approval |
| `/whatsetter:why` | Explain why an agent sent a message and propose the smallest fix, changed only with your approval |
| `/whatsetter:instagram` | Read-only Instagram report: posts that bring views and meetings, automations, planned posts, best times |
| `/whatsetter:import` | Import contacts into a WhatsApp list after a preview |
| `/whatsetter:bookings` | Meetings booked by the agents, per agent |
| `/whatsetter:campaign` | Show, pause or resume an agent by name |

## Safety

- **Nothing goes out without your yes.** Every tool that reaches a real person or goes public (sending a message, pushing a contact to an agent, publishing or scheduling an Instagram post, switching an automation on) makes the agent show you exactly what, to whom and when, and wait for your confirmation. Changing an agent's prompt or knowledge, which is live on its next message, asks too. Every tool carries `readOnlyHint` / `destructiveHint` annotations, so clients can auto-approve reads and always prompt on writes.
- **Anti-ban is enforced by the WhatSetter API, not by a prompt.** A new WhatsApp contact can't be messaged cold: first contact always goes through a list and an agent. Each WhatsApp number has a shared daily message budget. Every WhatsApp send replays a human rhythm (read receipt, typing, paced send). On Instagram and Messenger, the API only answers within 24 hours of the person's last message, as Meta requires. Every send carries an idempotency key, so a retry by the MCP server is very unlikely to send it twice.
- **The agents keep working.** On WhatsApp, a message sent through the plugin does not pause the agent: it keeps answering the lead and sees your message in the history. On Instagram and Messenger, it pauses the agent on that lead for the account's pause time, exactly like a reply typed in the inbox. Pausing a whole WhatSetter agent stops its replies too, so the plugin always confirms it with you first.

## Example prompts

- *"What happened in my inbox since yesterday, on every channel? Draft replies for the leads waiting on us, don't send anything."*
- *"Why did Julia tell Marie the offer ends on Friday?"*
- *"Which of my Instagram posts this month brought meetings, not just views?"*
- *"Plan a reel for tomorrow at the best time, with an automation on the word GUIDE. Show me everything first."*
- *"Import this CSV into a new list called November webinar."*
- *"How many meetings did my agents book this week, per agent?"*
- *"Every time a lead is qualified, send it to https://hooks.example.com/crm."*

## Network endpoints and credentials

This plugin ships no code, hooks or scripts: only markdown and one MCP server URL.

| Endpoint | Purpose |
|---|---|
| `https://mcp.whatsetter.com/mcp` | Hosted WhatSetter MCP server (Streamable HTTP) |
| `https://mcp.whatsetter.com/authorize`, `/token`, `/register` | OAuth 2.1 with PKCE and dynamic client registration |

Credential: a WhatSetter API key (`ws_live_…`), entered on the WhatSetter consent page, never in this repository or in your client configuration.

## Requirements

A WhatSetter workspace at [app.whatsetter.com](https://app.whatsetter.com). Sending on WhatsApp and running WhatsApp agents need at least one connected WhatsApp number; Instagram and Messenger need the account connected in the dashboard.

## Links

- Documentation: [docs.whatsetter.com/developers/mcp](https://docs.whatsetter.com/developers/mcp/)
- API keys and scopes: [docs.whatsetter.com/developers/authentication](https://docs.whatsetter.com/developers/authentication/)
- Anti-ban protections: [docs.whatsetter.com/dashboard/anti-ban](https://docs.whatsetter.com/dashboard/anti-ban/)
- Privacy policy: [whatsetter.com/privacy](https://whatsetter.com/privacy)
- Support: [contact@whatsetter.com](mailto:contact@whatsetter.com)

## License

MIT
