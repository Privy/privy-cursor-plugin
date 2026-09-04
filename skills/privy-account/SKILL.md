---
name: privy-account
description: >-
  Use when asking about marketing data in a Privy account — email and SMS
  campaigns, flows, displays (popups and forms), segments, contacts and
  consent, coupons, orders, products, account setup, or performance metrics.
  Hosted Privy MCP: OAuth, read-only, paid plan. Live tools are search →
  describe → execute (not the old list_*/get_* names).
---

# Privy account

Your Privy data, in the agent. Analyze email, SMS, campaigns, flows, displays, contacts, orders, products, segments, and revenue with live data from the merchant's Privy account.

Privy connects the agent to the marketing data in the connected account. Ask natural-language questions about email and SMS campaigns, flows, website popups and forms, audience segments, contacts and consent, coupons, orders, products, account setup, and performance metrics. The agent can compare campaign results, identify top-converting displays, summarize flow performance, inspect segment definitions and sizes, review order and revenue trends, and help understand what is configured across the store.

Use it for ecommerce marketing analytics, email and SMS performance reporting, lifecycle marketing reviews, and account audits.

All tools are **read-only**: retrieve and analyze Privy data, but do not change campaigns, contacts, flows, or account settings. Access is scoped to the **paid** Privy business selected during secure OAuth sign-in. No API token or custom OAuth application is required.

Server: `https://mcp.privy.com/mcp` (streamable HTTP, OAuth, scope `mcp_read`).

Never fabricate data. Report only what tools return; if a field or list is empty or missing, say so. List endpoints are paginated — check the pagination block before claiming totals.

## How to call tools

The live server is a **generic** toolset. Prefer this loop every time:

1. `search` — find a GET endpoint by keyword (campaign, flow, popup, segment, contact, order, coupon, revenue, …).
2. `describe` — full contract for that `method` + templated `path`. **Read it before execute**; it carries semantics (e.g. flow exit/re-entry).
3. `execute` — run the GET with `path_params`, `query`, and optional `Privy-Account-Id` in `headers`.

Also available:

| tool | when |
| --- | --- |
| `get_identity` | who authorized this token and which account it is pinned to |
| `search_help_docs` | how-to / setup / strategy about using Privy itself |
| `read_help_article` | full help article; pass a Page path from `search_help_docs` |
| `request_capability` | user asked for a write or something no endpoint covers — log the gap, then tell the user |
| `submit_feedback` | user wants to report a problem or missing capability; pass their words |

On `search` / `describe` / `execute` / help / identity / feedback: pass `telemetry.agent_thinking` every call. Pass `telemetry.user_intent` (and `user_frustration`) only on the first tool call after a new user message.

How a merchant's own flow or campaign behaves (exit, re-entry, delays, triggers) is per-object config — read that object via `execute`, not the help center.

Do **not** call the old named tools (`list_campaigns`, `get_flow`, …). If a directory or stale doc lists them, ignore it; trust `tools/list` and `search`.

## Endpoints (live `search`, 2026-09-01)

Account: `GET /v1/account`, `GET /v1/account/setup`, `GET /v1/identify` (`get_identity`)

Campaigns: `GET /v1/campaigns`, `GET /v1/campaigns/{id}`

Displays: `GET /v1/displays`, `GET /v1/displays/{id}`

Flows: `GET /v1/flows`, `GET /v1/flows/{id}`

Segments: `GET /v1/segments`, `GET /v1/segments/{id}`, `GET /v1/segments/{id}/contacts`

Contacts: `GET /v1/contacts`, `GET /v1/contacts/{id}`

Imports: `GET /v1/imports`, `GET /v1/imports/{id}`

Orders: `GET /v1/orders`, `GET /v1/orders/{order_id}`

Products: `GET /v1/products`, `GET /v1/products/{id}`

Coupons: `GET /v1/coupons`, `GET /v1/coupons/{id}`, `GET /v1/coupons/{id}/codes/{code}`

Reports (use these for metrics; do not derive numbers elsewhere):
`GET /v1/reports/summary`,
`GET /v1/reports/displays`, `GET /v1/reports/displays/{display_id}`,
`GET /v1/reports/flows`, `GET /v1/reports/flows/{flow_id}`,
`GET /v1/reports/campaigns`, `GET /v1/reports/campaigns/{campaign_id}`,
`GET /v1/reports/top_products`

If `search` returns something new, trust `tools/list` + `search` over this table.

## Presenting results

`PrivyResource = { kind, id, name, stats?, members? }`

`kind`: account | campaign | display | flow | segment | contact | order | product | coupon | import

Identify entities by human-readable fields, not raw ids:

- campaigns, displays, products: `title`
- flows: `title` plus `trigger_label` (titles can misstate the trigger)
- segments: `name`
- contacts: `email`
- orders: email plus date and amount

Ids are for chaining `execute` calls unless the user asks for them.

Subaccounts: execute `GET /v1/account`, then pass a subaccount `id` as `Privy-Account-Id` on later calls. Omit it for the main account.
