---
description: Report the meetings booked by the WhatSetter agents, grouped by agent.
argument-hint: "[optional: period, e.g. 'this week', or agent name]"
---

Report my WhatSetter bookings for: $ARGUMENTS

Default scope when empty: bookings made in the last 7 days, all agents.

Follow the `whatsetter` skill, "Results and bookings" playbook. `list_bookings` with `since` keeps the meetings that start on or after that date: set it to the start of the period, then keep the bookings whose `created_at` falls in the period. If I ask about meetings happening in a period rather than booked in it, keep the ones whose `start_at` falls in it instead. Resolve agent ids to names with `list_campaigns`, and read every page before counting.

Show: total booked, total canceled (listed separately, never counted as booked), a per-agent table, then each upcoming meeting with date and time in my timezone and the attendee's name. State the period covered and which date it is counted on.
