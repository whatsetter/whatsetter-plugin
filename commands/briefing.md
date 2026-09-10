---
description: Read-only WhatSetter briefing: new replies, leads waiting on us, meetings booked, campaign and number health.
argument-hint: "[optional: period or campaign name, e.g. 'since Monday' or 'Dubai']"
---

Give me a WhatSetter briefing for: $ARGUMENTS

Default scope when empty: the last 24 hours, all campaigns.

Follow the `whatsetter` skill, "Daily briefing" playbook. This command is read-only: send nothing, change nothing.

Deliver, in this order:

1. The numbers for the period: replies received, leads waiting on us, leads qualified, meetings booked, meetings canceled.
2. Hot conversations: the lead's name, their last words quoted, and one line saying what they want.
3. Leads waiting on us for hours, with how long they have waited.
4. Meetings booked, with date, time and campaign.
5. Health: campaigns that are paused and any campaign whose WhatsApp number looks disconnected.
6. Two or three suggested next actions. Ask before doing any of them.
