---
description: Report the meetings booked by the WhatSetter AI setter, grouped by campaign.
argument-hint: "[optional: period, e.g. 'this week', or campaign name]"
---

Report my WhatSetter bookings for: $ARGUMENTS

Default scope when empty: the last 7 days, all campaigns.

Follow the `whatsetter` skill, "Results and bookings" playbook. Use `list_bookings` with `since`, resolve campaign ids to names with `list_campaigns`, and read every page before counting.

Show: total booked, total canceled (listed separately, never counted as booked), a per-campaign table, then each upcoming meeting with date and time in my timezone and the attendee's name. State the period covered.
