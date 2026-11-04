---
description: Triage the WhatSetter inbox (WhatsApp, Instagram, Messenger) and draft replies. Nothing is sent without explicit approval.
argument-hint: "[optional: channel, agent name or period, e.g. 'Instagram' or 'Julia since yesterday']"
---

Triage my WhatSetter inbox for: $ARGUMENTS

Default scope when empty: the last 24 hours, every channel, all agents.

Follow the `whatsetter` skill, "Inbox triage" playbook. Sort conversations into: hot, waiting on us, booked, left for the owner (Instagram and Messenger `collab` and `support`), stopped, nothing to do. Quote each lead's last words and name the channel.

Draft replies only for hot, waiting and left-for-the-owner conversations, in the lead's language, in plain private-message style. On Instagram and Messenger, only draft when the lead's last message is less than 24 hours old, and say when that window closes. Show each draft with the lead's name and channel. Send nothing until I approve a specific draft. Never draft for a lead who asked to stop.
