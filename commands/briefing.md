---
description: Read-only WhatSetter briefing across WhatsApp, Instagram and Messenger: new replies, leads waiting on us, meetings booked, agent and account health.
argument-hint: "[optional: period, channel or agent name, e.g. 'since Monday', 'Instagram' or 'Julia']"
---

Give me a WhatSetter briefing for: $ARGUMENTS

Default scope when empty: the last 24 hours, every channel, all agents.

Follow the `whatsetter` skill, "Daily briefing" playbook. This command is read-only: send nothing, change nothing.

Deliver, in this order:

1. The numbers for the period, per channel when there is more than one: replies received, leads waiting on us, leads qualified, meetings booked, meetings canceled.
2. Hot conversations: the lead's name, the channel, their last words quoted, and one line saying what they want.
3. Leads waiting on us (`needs_reply`), with how long they have waited. On Instagram and Messenger, say when the 24-hour window to answer closes.
4. Meetings booked, with date, time and agent.
5. Health: paused agents, WhatsApp agents whose number is disconnected, Instagram accounts that must be reconnected.
6. Two or three suggested next actions. Ask before doing any of them.
