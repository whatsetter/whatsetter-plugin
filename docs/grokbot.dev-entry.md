---
type: plugin
name: "WhatSetter"
slug: whatsetter
tagline: "Work your WhatsApp AI setter from the agent: inbox, leads, bookings, campaigns"
category: sales
subcategory: outreach
install_steps:
  - "Create a WhatSetter workspace at https://app.whatsetter.com and connect a WhatsApp number."
  - "In the app, go to Settings → API → Create key. Pick the scopes you need (leads:read, conversations:read, bookings:read, campaigns:read for a briefing; messages:send only if the bot may reply for you). The key is shown once."
  - "Add the WhatSetter plugin from the Plugins tab, or add an MCP server to your bot: URL https://mcp.whatsetter.com/mcp (Streamable HTTP). OAuth is discovered automatically."
  - "On the first tool call, the Connect to WhatSetter page opens: paste the key and approve. Your bot only ever holds a revocable token, never the key."
  - "Ask: 'Give me my WhatSetter briefing'. Read-only tools are auto-approvable; anything that sends or changes something asks first."
prompt: "Connect to the WhatSetter MCP server at https://mcp.whatsetter.com/mcp. It exposes 23 tools for my WhatSetter workspace (AI appointment setter on WhatsApp). Start every session with whoami to learn my workspace and the scopes of my key, and only use what those scopes allow. Use the read-only tools freely (list_leads, get_lead, list_conversations, get_conversation_messages, list_campaigns, list_bookings, list_lists, list_groups, get_group, list_group_members, list_group_messages, list_group_events, list_webhooks) to answer questions about my leads, replies, meetings and campaigns; quote leads' actual words and never invent a reply, a booking or a number. For anything that writes or sends (update_lead, create_list, import_leads, pause_campaign, resume_campaign, create_webhook, delete_webhook, test_webhook and above all send_whatsapp_message), show me exactly what you are about to do and wait for my explicit approval. Never try to message a contact who was never contacted (the API refuses with lead_not_contacted): new contacts go into a list with import_leads and the campaign makes first contact at a safe pace. If the API answers quota_exceeded, stop sending on that number until midnight UTC. Drafts for WhatsApp are short plain text in the lead's language, no markdown."
works_with: ["WhatsApp", "HubSpot", "Zapier"]
project_url: "https://whatsetter.com"
repo_url: "https://github.com/whatsetter/whatsetter-plugin"
author:
  handle: "whatsetter"
  url: "https://whatsetter.com"
  platform: web
pricing_note: "Free plugin and MCP. Requires a WhatSetter workspace (plans at whatsetter.com)."
setup_minutes: 5
added_at: "2026-09-10T00:00:00Z"
updated_at: "2026-09-10T00:00:00Z"
status: proposed
---

## What it does

WhatSetter is an AI appointment setter on WhatsApp: campaigns contact imported leads at a human pace, the AI qualifies them in conversation and books meetings on your calendar (Cal.com, Calendly, GoHighLevel). This plugin connects your bot to that workspace over MCP (hosted server, OAuth 2.1, open-source plugin under MIT) and exposes 23 tools: list and update leads, read the inbox and full message history, send a WhatsApp message to an existing lead, create lists and import up to 500 contacts per call, list, pause and resume campaigns, read booked meetings, analyse managed WhatsApp groups (members, transcript, joins and leaves) and manage outbound webhooks (contact.qualified, booking.created, message.received…). Every tool carries `readOnlyHint` / `destructiveHint` annotations, so clients auto-approve reads and confirm writes. The bundled skill adds the operating rules: nothing sends without your yes, first contact always goes through a campaign, the daily quota per number is respected, and the bot never talks over the AI setter.

## Use it in Grok Bot

Ask in plain language: "give me my WhatSetter briefing", "who is waiting on us in the inbox?", "draft a reply to Karim but don't send it", "import this CSV into a new list called September webinar", "how many meetings did the setter book this week per campaign?", "pause the Dubai campaign", "who left my webinar group this week?". Anti-ban is enforced by the WhatSetter API itself, not by the prompt: cold outreach to an unknown number is refused, each WhatsApp number has a shared daily budget, and every send replays a human rhythm with an idempotency key so a retry can never double-send. Auth is one API key from app.whatsetter.com → Settings → API, pasted once on the WhatSetter consent page; your bot only holds a revocable token. Setup is the five steps above, about five minutes.
