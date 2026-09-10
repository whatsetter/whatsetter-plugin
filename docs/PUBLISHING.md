# Publishing runbook (maintainers)

How this plugin reaches each catalog, and what to redo on every release.

## Where it shows up

| Surface | How it gets there | Review | Cost per release |
|---|---|---|---|
| **Grok Bot → Plugins tab** and **Cursor → Plugins** | One submission of this repo at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish) (the Grok Bot tab is the Cursor Marketplace) | Manual, before listing and on every update | Re-review |
| **Grok CLI** (direct install) | `grok plugin marketplace add whatsetter/whatsetter-plugin` reads `.grok-plugin/marketplace.json` from this repo | None | None, tracks `main` |
| **Claude Code** | `/plugin marketplace add whatsetter/whatsetter-plugin` reads `.claude-plugin/marketplace.json` | None | None, tracks `main` |
| **xAI official catalog** (Grok CLI marketplace tab) | PR on [xai-org/plugin-marketplace](https://github.com/xai-org/plugin-marketplace), remote source pinned to a commit SHA | Code-owner review | One PR per promoted version |
| **grokbot.dev** (community directory) | PR adding `content/plugins/whatsetter.md` (draft in `docs/grokbot.dev-entry.md`) | Maintainer | None |
| **Grok Bot Marketplace** (bot templates, x.ai/bot/marketplace) | Build a "WhatSetter" bot on the listed plugin, *Share as template*, public link | Curated by the Grok Bot team | None |

## Before the first submission

1. **Official org.** The repo must live under the `whatsetter` GitHub organization, not a personal account: catalogs treat a branded plugin sourced from a personal account as possible impersonation. Add the logo, the `whatsetter.com` website and a verified domain (Organization settings → Verified and approved domains, one DNS TXT record) so first-party ownership is visible at a glance.
2. **Public repo, MIT.** Cursor requires open source. Add the topics `cursor-plugin`, `mcp`, `whatsapp`, `grok-bot`.
3. **Local test in Cursor.** `ln -s /path/to/whatsetter-plugin ~/.cursor/plugins/local/whatsetter`, then *Developer: Reload Window*. Check in *Customize* that the plugin, its MCP server and the `whatsetter` skill are listed. In chat, ask "who am I on WhatSetter?": the OAuth page must open, and `whoami` must return the workspace.
4. **Live MCP checks.** `https://mcp.whatsetter.com/.well-known/oauth-protected-resource` and `/.well-known/oauth-authorization-server` answer 200; an unauthenticated `POST /mcp` answers 401 with a `WWW-Authenticate` header.
5. **Docs page.** `docs.whatsetter.com/developers/mcp/` (EN, FR, ES) documents the plugin install path and links back to this repo, in both directions.

## Submit to the Cursor Marketplace

1. Go to [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish) signed in with the WhatSetter Cursor account.
2. Give the repository URL. The listing reads `.cursor-plugin/plugin.json` (name, description, logo, tags) and `mcp.json`.
3. Wait for the manual review. Answer questions on the review thread from the org account.

## Publish a new version

1. Bump `version` in **all three** manifests (`.cursor-plugin/plugin.json`, `.grok-plugin/plugin.json`, `.claude-plugin/plugin.json`) and in `.grok-plugin/plugin-index.json` / the two `marketplace.json` files.
2. Add a `CHANGELOG.md` entry.
3. Commit, push to `main`. Cursor re-reviews the update; Grok CLI and Claude Code users get it on their next update.
4. If listed in the xAI catalog: open a PR bumping the pinned `sha` (never a branch or tag), then `python3 scripts/generate-plugin-index.py` and `python3 scripts/validate-catalog.py` in their repo.

## Keep it reviewable

- No executable code, no hooks, no scripts, no `postinstall`: markdown and JSON only. Reviewers audit for supply-chain risk.
- Declare every network endpoint and credential in the README (already done).
- Keywords and `domains` stay brand-scoped (`whatsetter`, `whatsetter.com`): generic terms like `whatsapp` or `sales` get pushed back in the xAI catalog because they mis-fire the plugin suggestion prompt. Cursor `tags` are a different field and can carry `sales`, which is what groups plugins in the Sales section.
