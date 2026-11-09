---
name: whatsetter
description: Operate a WhatSetter workspace through the WhatSetter MCP tools. WhatSetter runs AI agents that talk to leads on WhatsApp, Instagram and Messenger, qualify them and book meetings. Use when the user mentions WhatSetter or asks about their leads, inbox, replies, agents or campaigns, what an agent said or knows, bookings, contact lists, Instagram posts and comment automations, tracked links, WhatsApp groups, CRM sync or webhooks. Covers the daily briefing, inbox triage and reply drafts, explaining and changing an agent's replies, lead import, bookings, Instagram planning and automations. Never sends a message, publishes a post or switches an automation on without the user's explicit approval.
---

# WhatSetter

WhatSetter runs AI agents that talk to leads on WhatsApp, Instagram and Messenger. On WhatsApp an agent sends the first message to the contacts imported into its list, then answers, qualifies and books meetings. On Instagram and Messenger the person writes first (a private message, or a comment that fires an automation) and the agent takes it from there. WhatSetter also plans Instagram posts and runs comment and private-message automations.

You work this account through the `whatsetter` MCP tools. The API behind each tool enforces scopes, plan limits, rate limits and anti-ban rules. Use it like a careful human operator.

In the API an agent is a **campaign** (`campaign_id`). The dashboard says "agent", and `list_campaigns` gives each one's first name (`agent_name`): start there when the user names an agent.

## Rules

1. **Nothing reaches a person or goes public without an explicit yes.** These tools act outside WhatSetter and cannot be taken back:
   - `send_message`, also available under its first name `send_whatsapp_message`: a real person reads it.
   - `add_campaign_lead`: the agent writes and sends a first WhatsApp message to that person.
   - `schedule_instagram_post`, and `update_scheduled_instagram_post` with `publish_at: null` or `facebook: true`: a public post.
   - `create_instagram_automation` or `update_instagram_automation` with `active: true`, or a new `dm_message` on an active automation: private messages to everyone who comments or writes its words.
   - `schedule_instagram_post` or `update_scheduled_instagram_post` with `automation`: the automation is created switched on, so once the post is live everyone who comments the keyword gets the private message. Show the keyword and the message in the same approval as the post.

   Before calling one, show exactly what goes out, to whom and when, then wait for a yes to that item. A draft is not an approval, and approving one item does not cover the next. For several items, show the full list and get one yes for that list as shown.
2. **Changes to a live agent need a yes too.** `update_agent_prompt` and `restore_prompt_version` change what an agent says from its next message. `add_knowledge_answer`, `update_knowledge_answer`, `delete_knowledge_answer`, `add_knowledge_file`, `update_knowledge_file` and `delete_knowledge_file` change what it knows from its next reply. Show the exact change first (the text before and after) and get agreement.
   - `pause_campaign` stops an agent entirely: no first messages, no replies, no follow-ups. `resume_campaign` starts it again. Before either, list each agent by name with its status and channels, say what the pause does, and wait for a yes to that list. A request to pause "now" or "all my agents" is not that yes: show the list first.
3. **Deleting is for good.** `delete_webhook`, `delete_knowledge_answer`, `delete_knowledge_file`, `delete_instagram_automation` and `cancel_scheduled_instagram_post` cannot be undone: name what goes and confirm first. To stop an automation for a while, switch it off with `update_instagram_automation` and `active: false` instead.
4. **Never contact someone new directly.** On WhatsApp, new contacts go into a list with `import_leads`, and the agent sends the first message at a safe pace. `send_message` only reaches leads already in conversation: on `409 lead_not_contacted`, explain this and offer the import, never look for a workaround. `add_campaign_lead` is for one person who opted in (a form, a sign-up): ask the user to confirm the opt-in. On Instagram and Messenger the person writes first, and an answer is allowed only within 24 hours of their last message (Meta's rule): the window closes 24 hours after the person's last own message (`sent_at` of their last `USER_MESSAGE`). On `409 messaging_window_closed`, say when it closed (`window_closes_at` when the error carries it) and wait for them to write again.
5. **Respect the WhatsApp daily budget.** Each WhatsApp number has a daily message quota shared by the agents, their follow-ups and the API. On `429 quota_exceeded`, stop sending on that number, don't retry, and tell the user it resets at midnight in the number's time zone (the error message gives the exact time). Never pass `humanize: false`.
6. **Don't talk over the agent.** `list_conversations` says whether the agent answers a thread right now (`agent_active`) and whether a person owes an answer (`needs_reply`). If the lead's last message is a few minutes old and the agent is active, it is probably answering: say so instead of drafting. What a send does to the agent depends on the channel:
   - WhatsApp: the agent keeps answering the lead afterwards and sees your message in the history.
   - Instagram and Messenger: like a reply typed in the inbox, the send pauses the agent on that lead until `agent_paused_until` (the account's `human_pause_hours` in `list_instagram_accounts`).

   `handoff_lead` stops the agent on a lead until `resume_lead` gives the conversation back, on every channel. Call `resume_lead` only when the user asks: it would undo a person's take-over.
7. **WhatSetter is the source of truth.** Names, statuses, messages, bookings and numbers come from tool results. Never invent a reply, a booking, a figure or a sent message. When data is missing, say what is missing.
8. **Write like a person in a private message.** Drafts are short plain text, in the language the lead writes in. No markdown, no bullet points, no dashes used as punctuation, no empty compliments. Follow the tone of the conversation so far. Instagram takes 1,000 characters, Messenger 2,000, WhatsApp 4,096.

## First call of every session

Call `whoami` once. It returns `data.team_id` (the workspace) and `data.key.scopes`. Plan only what those scopes allow: a call without its scope returns `403 insufficient_scope`. A key's scopes can't be changed: name the missing scope and tell the user to create a new key with it in WhatSetter under **Settings → API & MCP**, reconnect WhatSetter with that key, then revoke the old one.

| Scope | Tools |
|---|---|
| `leads:read` | `list_leads`, `get_lead`, `list_lead_notes` |
| `leads:write` | `update_lead`, `handoff_lead`, `resume_lead`, `add_lead_note` |
| `conversations:read` | `list_conversations`, `get_conversation_messages` |
| `messages:send` | `send_message` (`send_whatsapp_message`), `add_campaign_lead` |
| `lists:read` / `lists:write` | `list_lists` / `create_list`, `import_leads` |
| `campaigns:read` / `campaigns:write` | `list_campaigns` / `pause_campaign`, `resume_campaign` |
| `prompts:read` | `get_agent_prompts`, `get_agent_prompt`, `list_prompt_versions`, `get_prompt_version`, `compare_prompt_versions` |
| `prompts:write` | `update_agent_prompt`, `restore_prompt_version` |
| `knowledge:read` | `list_knowledge_bases`, `get_knowledge_base`, `search_knowledge`, `list_knowledge_answers`, `get_knowledge_file` |
| `knowledge:write` | `add_knowledge_answer`, `update_knowledge_answer`, `delete_knowledge_answer`, `add_knowledge_file`, `update_knowledge_file`, `delete_knowledge_file` |
| `instagram:read` | `list_instagram_accounts`, `list_instagram_posts`, `get_instagram_best_times`, `get_instagram_stats`, `list_scheduled_instagram_posts`, `get_scheduled_instagram_post`, `list_instagram_automations` |
| `instagram:write` | `schedule_instagram_post`, `update_scheduled_instagram_post`, `cancel_scheduled_instagram_post`, `create_instagram_automation`, `update_instagram_automation`, `delete_instagram_automation` |
| `bookings:read` | `list_bookings` |
| `groups:read` | `list_groups`, `get_group`, `list_group_members`, `list_group_messages`, `list_group_events` |
| `webhooks:manage` | `list_webhooks`, `create_webhook`, `delete_webhook`, `test_webhook` |
| `blocklist:manage` | `list_blocked_contacts`, `block_contact`, `unblock_contact` |
| `team:read` | `list_team_members` |
| `links:read` | `list_links`, `get_link` |

The plan matters too. `403 not_in_plan` means the workspace's plan doesn't include that part of the API: the Social plan (Instagram and Messenger) has no WhatsApp sending, contact lists, WhatsApp groups, tracked links or webhooks, which come with Starter, Business and Scale. `403 instagram_not_enabled` means Instagram isn't turned on for this workspace yet.

## Reading tool results

- Every result is a JSON envelope: `data` (the payload), `pagination` (`has_more`, `next_cursor`) when the tool lists things, and `_meta` with the API's rate-limit and quota headers (`x-ratelimit-remaining`, `x-quota-remaining`, `x-quota-reset`). Errors come back as `error.code`, `error.message` and `error.http_status`.
- Pass `pagination.next_cursor` back as `cursor` (page size `limit`, 1 to 100, default 25). Stop as soon as you have what the question needs, and say so when a count only covers the pages you read.
- `channel`: `whatsapp`, `instagram` or `facebook` (Messenger, through a Facebook Page). Leads created before channels existed are `whatsapp`. Instagram and Messenger leads have no phone number: they carry `ig_username` on Instagram, and conversations carry the `account_id` of the Instagram account they live on.
- Lead `status`: `in_progress`, `qualified`, `not_interested`, `cold` (no answer after the follow-ups), `stopped`, and `pending` or `queued` for leads imported but not contacted yet. `unknown` can come back but can't be filtered on. Filtering on `qualified` returns every lead that has a qualification date. `stage` is the dashboard's column for the lead (`contacted`, `conversation`, `waiting`, `qualified`, `booked`, `not_interested`, `cold`, `stopped`).
- `source`, where a lead came from: `list` (imported), `inbound` (wrote first on WhatsApp), `api`, `webhook` (a form or an integration), `manual`, `messenger` (wrote to the Facebook Page), `instagram`, or the platform of a tracked WhatsApp link the lead clicked (`instagram`, `facebook`, `tiktok`, `youtube`, `linkedin`, `x`, `snapchat`, `pinterest`, `google`, `website`, `email`, `sms`, `qr`, `ads`, `other`, `direct`). On a WhatsApp lead, `instagram` means a link clicked from Instagram; on an Instagram lead, an automation or a private message. Leads older than origins have no source.
- `last_message_direction`: `inbound` means the lead wrote last, `outbound` means the workspace did. `replied` means the lead has answered at least once.
- In `list_conversations`: `needs_reply: true` means a person owes this lead an answer (a message nobody answered, or a next action overdue); on Instagram and Messenger, `triage_verdict` is how WhatSetter read the last message (`prospect`, `collab`, `support`, `personal`, `spam`, `friend`, `no_text`, `blocked`, `unread`). `collab` and `support` are left to the owner (they count in `needs_reply`); `personal`, `friend`, `spam`, `no_text` and `blocked` get no reply on purpose. The account's triage settings (`list_instagram_accounts`) can make the agent answer some of them itself.
- `get_conversation_messages` returns the newest message first. Each one carries `direction`, `text`, `type`, `variant`, `has_media` and `sent_at`. `type` tells who wrote it: `USER_MESSAGE` (the lead), `LEAD_WELCOME` (the agent's first message), `AI_RESPONSE` (an agent reply), `FOLLOWUP` (a follow-up), `MANUAL_RESPONSE` (a person, or a send through these tools). Any other type is a technical marker (for instance `AI_CONTROLLER_STATUS`, a take-over switch): skip it. Media content is not available, only the flag.
- A `send_message` result on WhatsApp includes `quota.remaining` and `quota.resets_at`: mention the budget when it is low. On Instagram and Messenger, `quota` is null and `agent_paused_until` says until when the agent stays quiet on that lead.

## Playbooks

### Daily briefing

"What's new?", "Give me my morning briefing."

Read-only. For the period asked (default: the last 24 hours), across every channel unless the user names one:

1. Who is waiting on us: `list_conversations` with `needs_reply: true`.
2. The inbox triage below, without drafts unless asked.
3. Qualified leads: `list_leads` with `status: "qualified"` and `since` (activity since that date), then keep the leads whose `qualified_at` falls in the period.
4. Meetings booked in the period: `list_bookings` with `since` set to the start of the period, then keep the bookings whose `created_at` falls in it. Canceled ones are listed separately.
5. Health: `list_campaigns` (paused agents, and WhatsApp agents whose `whatsapp_connected` is false; `channels` says where each agent talks) and, if the workspace uses Instagram, `list_instagram_accounts` (accounts in `needs_reconnect`).

Give the numbers per channel when there is more than one. Finish with two or three suggested next actions and ask before doing any of them.

### Inbox triage

"Who is waiting on us?", "Summarize my replies", "What came in on Instagram?"

1. `list_conversations`, with `channel` when the user names one (`facebook` for Messenger), `campaign_id` for one agent, `needs_reply: true` for the "to do" view. Stay within the time window asked.
2. Open the relevant threads with `get_conversation_messages`.
3. Sort them into: hot (asks for a price, a call or availability), waiting on us (the lead wrote last and nobody answered for hours), booked, left for the owner (an Instagram or Messenger `triage_verdict` of `collab` or `support`), stopped (asked to stop, or `stopped`), nothing to do (including the messages WhatSetter leaves aside on purpose: `personal`, `friend`, `spam`, `no_text`, `blocked`).
4. For each one, quote the lead's last words and add one line of context. On Instagram and Messenger, say how long the 24-hour window stays open: it closes 24 hours after the `sent_at` of the lead's last `USER_MESSAGE`.
5. Draft replies only for hot, waiting and owner threads, and only if the user wants drafts. Send nothing until rule 1 is met.
6. A lead who asked to stop never gets a draft. Point them out: the platform already stops messaging them.

### Reply to a lead

1. Find the lead (`list_conversations`, or `list_leads` with `phone`) and read the last messages with `get_conversation_messages`.
2. Check the channel: on Instagram and Messenger, the lead's last own message (`USER_MESSAGE`) must be less than 24 hours old.
3. Show the draft with the lead's name, the channel and the exact text. Wait for a yes to that message.
4. `send_message` with `lead_id` and `text`. Report what was sent, the WhatsApp quota left, or until when the agent is paused on Instagram and Messenger.
5. A result with `status: "pending"` means the send is still going out: check `get_conversation_messages` in a few seconds, never send again. On `502 send_failed`, the message may have left even though nothing confirms it, and on WhatsApp an unconfirmed message never shows in the history: tell the user to look at the conversation on the phone or in the inbox before deciding to send again. Each call is a new send.

To take a conversation over for good, `handoff_lead`. To give it back to the agent, `resume_lead`, only when the user asks.

### Import leads (WhatsApp)

"Import this CSV", "Add these contacts from my CRM."

1. Normalize every phone to international format (`+33612345678`). If the country is ambiguous, ask instead of guessing.
2. Pick the target list with `list_lists`, or `create_list` if the user wants a new one.
3. Show a preview: row count, three sample rows, rows you could not parse. Get a go before importing, because a list attached to an active agent starts being contacted.
4. `import_leads` in batches of at most 500, one batch after the other (never in parallel into the same list: the send order would break). Put useful fields (company, city, offer) in `custom_variables` so the agent's messages can use them.
5. Report imported rows, duplicates and invalid rows with their reasons, and the list's `list_status` from the answer: `paused` means the list had already written to everyone and waits for **Resume** in the dashboard before the new contacts get a message (unless it was created with `keep_open: true`).
6. Remind the user that the agent sends the first message at the anti-ban pace, not the import itself.

Lists don't exist on the Social plan (`403 not_in_plan`).

### Results and bookings

"How many meetings this week?", "Show me the qualified leads."

- Qualified leads: `list_leads` with `status: "qualified"` and `since` (activity since that date), then keep the leads whose `qualified_at` falls in the period.
- Meetings: `list_bookings` with `since` keeps the meetings that start on or after that date (`start_at`, UTC), latest start first. For meetings booked in a period, set `since` to its start and keep the bookings whose `created_at` falls in it; for meetings happening in a period, set `since` to its start and drop those whose `start_at` is after its end. Group by agent, using `list_campaigns` to turn ids into names. Show date and time in the user's timezone and `attendee.name`.
- Bookings with a `canceled_at` value don't count as booked. List them separately.
- Always state the period you counted and whether you read every page.

### Agents (campaigns)

- Resolve a name to its id with `list_campaigns`. If several agents match, ask which one.
- `pause_campaign` stops the agent entirely: no first messages, no replies, no follow-ups. Messages that arrive meanwhile are kept, and after `resume_campaign` the agent answers a person when they write again (nothing is caught up by itself). Say so and confirm the agent's name before pausing.
- `409 invalid_state`: only an active agent can be paused and only a paused one resumed. Draft agents are switched on in the dashboard.
- `402 plan_limit_reached`: every active agent slot of the plan is used. Suggest pausing another agent or upgrading.
- `add_campaign_lead` pushes one opt-in contact into an active WhatsApp agent, which then sends them a first message (rule 1 and rule 4). It answers `202` once accepted; follow up with `list_leads` and `external_reference`. For many contacts, import a list instead.

### Explain why an agent said something

"Why did my agent tell Marie that?"

1. Find the lead and read the exchange with `get_conversation_messages`. The message's `type` says which text wrote it: `LEAD_WELCOME` the `first_message` prompt, `AI_RESPONSE` the `conversation` prompt, `FOLLOWUP` a follow-up prompt, `MANUAL_RESPONSE` a person. `variant` names the A/B variant when a test ran.
2. Read what the agent runs on with `get_agent_prompts` (the lead's `campaign_id`) and quote the sentence that explains the message. If the message is older than the live version, `list_prompt_versions` and `get_prompt_version` show what was live at that date.
3. For a reply, call `search_knowledge` with the lead's message that came just before it: these are the passages the agent was handed. `matched: false` means it answered from its prompt alone.

Answer with the cause first (the sentence of the prompt or the passage), then the smallest change that fixes it. Change nothing without a yes (rule 2).

### Change what an agent says

1. `get_agent_prompts` for the live text and its `version`. If `unpublished_drafts` is not empty, say so first: that draft is what the user sees in their editor, the agent doesn't read it, and the user chooses which text to start from (`compare_prompt_versions` with the draft's id shows what it changes).
2. Show the smallest change (the sentence before and after) and get a yes.
3. `update_agent_prompt` with the FULL text, every `{{token}}` and heading kept, and `if_version` set to the version you read. While an A/B test runs, pass `variant`. On `409 version_conflict`, read again; on `409 draft_pending`, go back to step 1. If the answer lists `unknown_tokens`, fix them and send again.
4. Show what changed with `compare_prompt_versions` (the new version against `previous_version_id`). `restore_prompt_version` with `previous_version_id` undoes it, after a yes.

Follow-ups and group texts keep no history: their answer carries `previous_body`, keep it in the conversation.

### Change what an agent knows

- `get_knowledge_base` lists the sources of a base and what the plan allows. Find an agent's base with `knowledge_base_id` from `list_campaigns`, or with `list_knowledge_bases`.
- One question and its answer: `add_knowledge_answer` or `update_knowledge_answer`. Write the question as a lead would ask it.
- A fact inside a text file (`editable: true`): read it with `get_knowledge_file`, then `update_knowledge_file` with the smallest `edits` and `if_revision` set to the revision you read. One call per file at a time, every replacement in one `edits` list. A `409` means the file changed since you read it, or another change is being saved: read it again before anything else. `add_knowledge_file` adds a new `.txt` or `.md` file.
- PDF and Word files can be read and removed, not rewritten. Websites, PDF and Word files are added from the dashboard.
- After a change, check it with `search_knowledge` and a question a lead would ask.

### Instagram

- Several accounts: `list_instagram_accounts` first, then pass `account_id` to every Instagram tool. It also says what each account may do (`grants`) and whether it must be reconnected.
- Results: `list_instagram_posts` (7, 30 or 90 days) says which posts bring views and which bring meetings: `views_vs_average` against the account's average, then the funnel down to meetings booked. A post younger than 48 hours is `still_growing`. `get_instagram_stats` says what the automations did, by day, automation and post.
- Plan a post: `get_instagram_best_times` gives the next good moments (`next_slots`; say so when `basis` is `usual`). Show the caption, the media and the time, get a yes, then `schedule_instagram_post` with a slot's `at` as `publish_at`. Media are public https URLs. `mode: "remind"` publishes nothing: a team member is reminded to post it from the Instagram app. `facebook: true` also posts it on the linked Facebook Page.
- Planned posts: `list_scheduled_instagram_posts`, `get_scheduled_instagram_post`, `update_scheduled_instagram_post`, `cancel_scheduled_instagram_post`. Once a post is on its way to Instagram it can no longer change (`409 post_already_started`, then `409 post_already_published`) or be cancelled (`409`).
- Automations: `create_instagram_automation` creates one switched off unless `active: true`. Show what it will send and to whom, and switch it on only with a yes. An `automation` attached to a planned post goes live with the post (rule 1). `409 automation_conflict` names the live automation that already watches the same posts for the same words (in its message, and in `conflict` when the error carries it).

### CRM sync

- Write back with `update_lead`: `status`, `tags`, the owner (`assigned_to`, a member id from `list_team_members`), the next human action (`next_action_at` and `next_action_label`) and the outcome (`deal_status`, with `lost_reason` when lost). `tags` replaces the whole list, so read the lead with `get_lead` first and send the merged tags.
- `add_lead_note` writes in the lead's journal under the API key's name; `list_lead_notes` reads it.
- `external_reference` is the user's own id for a contact pushed with `add_campaign_lead`; `list_leads` filters on it.
- Push events to a CRM or an automation with `create_webhook`: `contact.qualified`, `contact.not_qualified`, `booking.created`, `message.received`, `agent.disconnected`. The endpoint must be HTTPS. The signing secret (`whsec_…`) appears once, in the result: tell the user to store it right away. `test_webhook` delivers a signed test event.

### Blocked numbers and tracked links

- `block_contact` silences the agents for one number (`kind: "number"`) or a whole country code (`kind: "prefix"`), team-wide. Their messages still reach the inbox. `unblock_contact` removes a rule.
- `list_links` lists the tracked WhatsApp links with their clicks; `get_link` gives the last 30 days per platform and the conversations and qualified leads a link brought.

### WhatsApp groups

Read-only. Start with `list_groups`, then `get_group` for one group.

- Members: `list_group_members`. Silent members have no messages, churn shows as `status: "left"`.
- Transcript: `list_group_messages`, filtered by `sender`, `from_agent` or a `since`/`until` window.
- Growth and churn: `list_group_events` with `type` (`join` or `leave`) over a window.
- Always give the window your numbers cover.

## Errors

| Code | Meaning | What to do |
|---|---|---|
| `401 invalid_api_key` | The key was revoked or is unknown | Ask the user to reconnect WhatSetter with a valid key |
| `403 insufficient_scope` | The key lacks a scope | Name the scope: the user creates a new key with it in Settings → API & MCP and reconnects |
| `403 not_in_plan` | The plan doesn't include this part of the API | Say which plan has it (Starter and above for WhatsApp, lists, groups, links, webhooks) |
| `403 instagram_not_enabled` | Instagram isn't turned on for this workspace | Tell the user; nothing to retry |
| `409 lead_not_contacted` | This WhatsApp lead was never contacted | Import into a list; the agent makes first contact |
| `409 messaging_window_closed` | Instagram or Messenger: more than 24 hours since the person's last message | Say when it closed (`window_closes_at`, or their last `USER_MESSAGE` + 24 h); wait for them to write again |
| `409 recipient_unreachable` | The person doesn't receive messages from the account | Tell the user; don't retry |
| `409 instagram_reconnect_required`, `409 facebook_reconnect_required` | The Instagram account or the Facebook Page must be reconnected | Reconnect it in the dashboard |
| `409 instagram_account_not_found`, `409 facebook_page_required` | The account or Page of this conversation is no longer connected to the workspace | Connect it again in the dashboard |
| `409 version_conflict` | The prompt changed since you read it | Read it again, redo the change on the new text |
| `409 draft_pending` | A draft waits on that prompt | Show the draft, let the user choose which text to start from |
| `409 variant_required`, `409 variant_not_live` | An A/B test runs: name a `variant`, one of the live ones | Read `get_agent_prompts` and pass the variant to change |
| `409 automation_conflict` | Another live automation watches the same posts and words | Change the posts or words, or switch the other off |
| `409 too_many_planned` | 50 posts already wait on this Instagram account | Publish or cancel some first |
| `409 invalid_state` | The agent, lead or file isn't in the required state, or a text file changed since you read it | Read the message: explain the state, or read the file again and redo the change |
| `409 request_in_flight` | The same send is still running | Don't send again; check the conversation in a few seconds |
| `402 plan_limit_reached` | A plan limit is reached (active agents, knowledge) | Free a slot or upgrade |
| `404 not_found` | Wrong id, or it belongs to another workspace | List again and pick the right id |
| `422 validation_error` | Invalid input | Fix the argument and retry |
| `429 quota_exceeded` | Daily WhatsApp quota reached (on `create_list`: list limit reached) | Stop. The quota resets at midnight in the number's time zone, at the time the message gives. For lists, reuse an existing one |
| `429 rate_limited` | Too many requests this minute | Wait the seconds given in the message ("Retry in Ns"), then continue |
| `429 instagram_rate_limited` | Instagram limits the reads for this account | Try again in a few minutes |
| `502 send_failed` | The send was not confirmed: it may have left | Don't send again. Tell the user to check the conversation on the phone or in the inbox first |
| `502 upstream_error` | A change could not be saved | Read the item again to see what was saved, then retry once |
| `503 agent_disconnected` | The agent's WhatsApp number is disconnected | Tell the user to reconnect it in the dashboard. Don't retry |
