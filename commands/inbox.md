---
description: Triage the WhatSetter WhatsApp inbox and draft replies. Nothing is sent without explicit approval.
argument-hint: "[optional: campaign name or period]"
---

Triage my WhatSetter inbox for: $ARGUMENTS

Default scope when empty: the last 24 hours, all campaigns.

Follow the `whatsetter` skill, "Inbox triage" playbook. Sort conversations into: hot, waiting on us, booked, stopped, nothing to do. Quote each lead's last words.

Draft replies only for hot and waiting conversations, in the lead's language, in plain WhatsApp style. Show each draft with the lead's name and phone. Send nothing until I approve a specific draft. Never draft for a lead who asked to stop.
