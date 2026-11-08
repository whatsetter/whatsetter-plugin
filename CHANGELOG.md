# Changelog

## 1.1.1 (2026-11-08)

- **Pausing or resuming an agent always waits for a yes.** A real test asked to pause "all my agents, now": the agent went straight to `pause_campaign`. The skill now lists the agents by name and waits for a yes to that list, as the README promised.

## 1.1.0 (2026-11-08)

- **Instagram and Messenger.** The skill and the commands now work the three channels WhatSetter agents talk on: the `channel` filter, the "to do" view (`needs_reply`), how WhatSetter sorted an Instagram or Messenger message (`triage_verdict`), Meta's 24-hour answer window, and the pause a send opens on Instagram and Messenger (WhatsApp keeps the agent answering).
- **Follows the hosted MCP server 1.6.0: 66 tools (23 in 1.0.0).** New in the skill: `send_message` on every channel, `handoff_lead` / `resume_lead`, lead notes, owner, next action and outcome, `add_campaign_lead`, an agent's prompts and their versions, knowledge bases (answers and text files), Instagram accounts, post results, best times, the post planner and automations, the blocklist, team members and tracked links.
- **Vocabulary fixed.** Lead statuses are the API's own (`in_progress`, `qualified`, `not_interested`, `cold`, `stopped`, `pending`, `queued`): 1.0.0 named statuses the API does not use. Lead origins (`source`) and the dashboard `stage` are documented.
- **Approval before anything goes out or goes live**, extended from WhatsApp messages to Instagram and Messenger messages, public posts, automations switched on, and changes to an agent's prompt or knowledge.
- New playbooks: reply to a lead, explain why an agent said something, change what an agent says, change what it knows, Instagram, blocked numbers and tracked links. The briefing and the triage cover every channel.
- New commands: `why` (explain an agent's message and propose the smallest fix) and `instagram` (read-only report). The five existing commands cover the three channels.
- README: the 20 scopes, the 66 tools by domain, what the Social plan opens, and the safety rules per channel.

## 1.0.0 (2026-09-13)

- Initial release: hosted WhatSetter MCP server (`https://mcp.whatsetter.com/mcp`, OAuth 2.1, 23 tools).
- `mcp.json` (read by the Cursor manifest) declares `"placement": "server"` so Grok Bot dials the hosted MCP server. Without it the connector is listed but never shows a connect card. (Added 2026-10-01.)
- `whatsetter` skill: send approval, anti-ban rules, inbox triage, lead import, bookings, campaigns, CRM sync and group analytics playbooks.
- Manifests for Cursor (`.cursor-plugin/`, plugin + marketplace), Grok (`.grok-plugin/`) and Claude Code (`.claude-plugin/`).
