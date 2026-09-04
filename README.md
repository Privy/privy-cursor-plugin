# Privy

Cursor / Agent plugin for [Privy](https://www.privy.com).

Wraps the hosted MCP at `https://mcp.privy.com/mcp` so agents can **read** a merchant account: campaigns, displays, flows, segments, contacts, orders, products, coupons, and reports.

The live server is a **generic** toolset: `search` → `describe` → `execute`, plus `get_identity`, help-center tools, `request_capability`, and `submit_feedback`. All data tools are read-only.

## Requirements

- A paid Privy plan
- An MCP client that supports remote HTTP + OAuth

No API token. First use opens a browser: log in to Privy and pick a business.

## What's in this plugin

- `plugin.json` — Agent Plugin manifest
- `mcp.json` — streamable HTTP server `privy` → `https://mcp.privy.com/mcp`
- `skills/privy-account` — when and how to call the live tools
- `assets/logo.png` — official 600×600 green-square logotype for Cursor Marketplace (1:1 plate)

## Local load

Copy this directory to `~/.cursor/plugins/local/privy` (a real directory, not a symlink out of that folder). Reload Window / Customize. Cursor prompts for Privy OAuth on first tool use.

## Docs

https://help.privy.com/docs/learn/mcp

https://www.privy.com/privacy

## License

MIT — Copyright (c) 2026 Privy Operations, LLC; see `LICENSE`.

## Marketplace

Submit flow: https://cursor.com/marketplace/publish  
Publisher terms: https://cursor.com/marketplace-publisher-terms
