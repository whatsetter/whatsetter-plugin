---
description: Show, pause or resume a WhatSetter agent (a campaign in the API) by name.
argument-hint: "<status|pause|resume> <agent name>"
---

Agent action requested: $ARGUMENTS

Follow the `whatsetter` skill, "Agents (campaigns)" playbook.

- Resolve the agent's name with `list_campaigns` (`agent_name` or `name`). If several agents match, ask which one.
- `status`: show its status, its channels, whether its WhatsApp number is connected (WhatsApp agents), and its leads by status from `list_leads` with `campaign_id`.
- `pause`: tell me that pausing stops the agent entirely (no first messages, no replies, no follow-ups; messages that arrive are kept and answered when the person writes again after a resume), show the agents that would be paused by name with their status, then stop and wait for my yes before calling `pause_campaign`. "All" or "now" in my request is not that yes. For several agents, one yes covers the list as shown.
- `resume`: show the agents that would be resumed by name, then wait for my yes before calling `resume_campaign`. On `402 plan_limit_reached`, say that every active agent slot of the plan is used and suggest pausing another agent or upgrading.
- On `409 invalid_state`, explain the agent's current status. Draft agents are switched on from the dashboard.
