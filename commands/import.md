---
description: Import contacts into a WhatSetter list after a preview. The WhatsApp agent attached to the list makes first contact at the anti-ban pace.
argument-hint: "<file, pasted rows or description of the contacts> [into <list name>]"
---

Import these contacts into WhatSetter: $ARGUMENTS

Follow the `whatsetter` skill, "Import leads (WhatsApp)" playbook.

1. Normalize phones to international format. Ask when a country code is ambiguous, never guess.
2. Pick the target list with `list_lists`, or create one with `create_list` if I named a new one.
3. Show a preview (row count, three sample rows, unparseable rows) and wait for my go. A list attached to an active agent starts being contacted.
4. Import in batches of 500 with `import_leads`, putting useful fields in `custom_variables`.
5. Report imported, duplicate and invalid rows with reasons.

Never message any of these contacts directly. First contact is the agent's job. Lists are WhatsApp only: on `403 not_in_plan`, say the workspace's plan has no contact lists (they come with Starter and above).
