---
description: Show, pause or resume a WhatSetter campaign by name.
argument-hint: "<status|pause|resume> <campaign name>"
---

Campaign action requested: $ARGUMENTS

Follow the `whatsetter` skill, "Campaigns" playbook.

- Resolve the campaign name with `list_campaigns`. If several campaigns match, ask which one.
- `status`: show its status, whether a WhatsApp number is connected, and its leads by status from `list_leads` with `campaign_id`.
- `pause`: confirm the exact campaign name with me, then call `pause_campaign`. Outbound sending stops.
- `resume`: call `resume_campaign`. On `402 plan_limit_reached`, say that every active campaign slot of the plan is used and suggest pausing another campaign or upgrading.
- On `409 invalid_state`, explain the campaign's current status. Draft campaigns are activated from the dashboard.
