# Changelog

## 1.0.0 (unreleased)

- Initial release: hosted WhatSetter MCP server (`https://mcp.whatsetter.com/mcp`, OAuth 2.1, 23 tools).
- `mcp.json` (read by the Cursor manifest) declares `"placement": "server"` so Grok Bot dials the hosted MCP server. Without it the connector is listed but never shows a connect card.
- `whatsetter` skill: send approval, anti-ban rules, inbox triage, lead import, bookings, campaigns, CRM sync and group analytics playbooks.
- Manifests for Cursor (`.cursor-plugin/`, plugin + marketplace), Grok (`.grok-plugin/`) and Claude Code (`.claude-plugin/`).
