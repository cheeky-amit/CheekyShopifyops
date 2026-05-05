---
id: meta.wiring
name: wiring
title: Wiring — combine these skills with other MCPs
description: How to add other MCPs to a project that uses these skills, or drop these skills into a project that already has other MCPs. Documentation, not integration.
audience: contributor
roles: [founder, technical, integrator]
category: meta
risk: read
mcp_tools: []
graphql:
  query: false
  mutation: false
triggers:
  - "wiring"
  - "how do I add another MCP"
  - "how do I use these skills with [Klaviyo / Notion / Slack / etc.]"
  - "drop these skills into my existing project"
  - "combine shopify with other tools"
inputs: []
outputs:
  summary: |
    A guided walkthrough — host-agnostic — for two scenarios:
    (a) adding other MCPs alongside this skill bundle,
    (b) dropping this skill bundle into a project that already has other MCPs.
status: stable
version: 1.0.0
---

## Purpose

This skill bundle is **strict Shopify MCP only**. But merchants often need a tool the Shopify MCP doesn't expose:

- Send transactional or marketing email → email MCP (Klaviyo, Mailchimp, Resend, …).
- Place a purchase order with a supplier → 3PL or supplier API.
- Sync to accounting → QuickBooks/Xero.
- Track campaigns → ads MCP.
- Operations / docs → Notion, Linear, Slack.

This `meta.wiring` skill is the **single documented place** that explains how to compose this bundle with other MCPs without breaking the safety contract.

It is documentation, not an integration. It does not call other MCPs. It explains the patterns.

## When to use

- A merchant asks "can I send a win-back email from this list?" — and you need to point at the wiring patterns.
- A contributor wants to drop these skills into an existing project that already has other MCPs.

## When NOT to use

- A merchant just wants the answer to a one-shot question. Use the regular Shopify skill — don't drag wiring into a simple ask.

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). This skill is documentation-only — there are no Shopify reads or writes after step 0; loading context just scopes the conversation to "this merchant, on this store" so wiring suggestions can reference the merchant's actual store name and stage.
1. Walk the merchant through the relevant scenario below.

## Two scenarios

### Scenario A — adding another MCP alongside this bundle

You started with `shopify-skills` and want to add (for example) Klaviyo.

1. **Install the new MCP in your client.** Each client (Claude Code, Claude Desktop, Cursor, etc.) has its own way of registering MCP servers. Check that client's docs. The MCP itself is server-side; clients just add a config entry pointing at it.
2. **Verify the new MCP is loaded.** In the new conversation, the new MCP's tools should be discoverable. If not, the client's MCP registration didn't take effect — restart the client.
3. **Use both bundles together.** Shopify skills produce data (e.g. `retention.win-back-list`); the new bundle's skills consume it (e.g. Klaviyo's "send to list" skill). The merchant's prompt can mix references.
4. **Don't write cross-bundle skills in this repo.** Cross-bundle skills (e.g., a skill that calls Shopify *and* Klaviyo) belong in a separate, project-specific skill bundle, not in `shopify-skills`. Mixing tools in this repo would break the strict-MCP scope and make the bundle harder to drop into other projects.

### Scenario B — dropping this bundle into a project that already has other MCPs

You have a project with (say) Notion + Slack + Klaviyo MCPs already wired. You want these Shopify skills to live alongside them.

1. **Clone or symlink** this repo's `skills/` folder into your project's skill directory. Most clients accept multiple skill paths — check the client docs.
2. **Don't move skills out of their category folders.** The IDs (`catalog.product-status-audit`, etc.) and folder structure are public surface; reorganizing locally will make pulling updates from upstream painful.
3. **Compose at the project level.** If you want a "weekly board pack" skill that pulls from Shopify + Notion + Slack, write it in *your* project repo, calling these skills as building blocks.
4. **Keep your overrides separate.** If you fork a Shopify skill and modify it, keep the modified copy in your project repo (with a different ID prefix, e.g. `mybrand.product-status-audit`) — don't edit `shopify-skills/` files in place. This makes upstream updates clean.

## Common compositions

These are described, not provided. Each requires the named external MCP to be installed in the client.

| Need | Shopify side | External side |
|---|---|---|
| Send win-back email | `retention.win-back-list` (build list) | email MCP (Klaviyo / Mailchimp / Resend) — feed list to "create campaign" + "send" |
| Sync analytics to BI | `analytics.daily-pulse` (export) | warehouse MCP (BigQuery / Snowflake / Postgres) — write into a sales fact table |
| Auto-place reorder PO | `inventory.low-stock-alert` (list low SKUs) | supplier/3PL MCP (varies) — submit PO; or Notion/Linear MCP for a manual PO ticket |
| Anniversary campaign | `retention.first-order-anniversary` (cohort) | email MCP — schedule an automated flow trigger |
| Stuck-order Slack alert | `orders.fulfillment-watchlist` (read) | Slack MCP — post message to ops channel |
| Board pack / weekly digest | `reporting.weekly-merchant-brief` (read) | Notion / Slack MCP — write to a page or channel |

## What this skill won't do

- **Install MCPs for you.** That's the client's job and depends on the client.
- **Maintain a list of "supported" external MCPs.** The MCP ecosystem is too fluid; this would rot. The patterns above work with any compatible MCP, named or not.
- **Bridge between MCPs at runtime.** Each call goes through the host's tool router; skills don't open direct connections to other MCP servers.

## Privacy and safety still apply

When composing across MCPs:

- The **strict-MCP rule** applies inside this repo only. Cross-MCP composition lives in your project, not here.
- The **safety contract** (preview → confirm → execute → log → rollback) applies to writes through *any* MCP, not just Shopify. If you write a cross-bundle skill, follow the same pattern.
- The **no-real-data-in-files** rule still applies in any repo Shopify skills live in. Composing with Klaviyo doesn't license you to commit subscriber lists to git.

## Examples

- [`examples/basic.md`](examples/basic.md) — describes a typical "Shopify + email MCP" composition without naming a specific email vendor (the same pattern works with any of them).
