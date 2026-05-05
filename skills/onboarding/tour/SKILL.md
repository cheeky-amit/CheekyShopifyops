---
id: onboarding.tour
name: tour
title: Tour the available skills
description: Read-only walkthrough of what Claude can do on this store, organized by category, with sample asks.
audience: merchant
roles: [founder, operations, marketing, finance, cx, merchandiser]
category: onboarding
risk: read
mcp_tools:
  - get-shop-info
  - graphql_query
graphql:
  query: true
  mutation: false
triggers:
  - "what can you do?"
  - "show me around"
  - "tour"
  - "list your skills"
  - "what skills are available"
inputs: []
outputs:
  summary: |
    Category-by-category list of available skills with a 1-line description
    and 2-3 sample asks per category. Ends with a CTA: "set me up" if not
    onboarded, or "what do you want to work on?" if already set up.
status: stable
version: 1.0.0
---

## Purpose

A merchant who hasn't worked with this skill bundle before doesn't know what they can ask. This skill answers that. It's pure read — no writes, no preview, no confirmation. Just a short, scannable tour, organized by category.

## When to use

- Merchant asks "what can you do?", "show me around", "tour", "list your skills".
- After a fresh install, before any other skill has run.
- After a long break, when the merchant wants a refresher.

## When NOT to use

- The merchant has a specific ask. Route to the matching skill.
- The merchant asks for setup. Route to `onboarding.first-run`.
- The merchant asks to change profile. Route to `onboarding.refresh-profile`.

## Workflow

1. **Step 0 — load context.** Call `_system.shop-context` to get `shop.pretty` and the `onboarding_state`. No writes follow this skill, so the rest is purely formatting.

2. **Render the tour.** Category-by-category, 1-line description plus 2–3 sample asks per category. Skip `_system` and `meta` (those are infrastructure, not merchant-facing).

   Categories to cover, in this order:
   - **catalog** — Audit and clean up your product catalog. Sample asks: "audit my products", "find products missing descriptions", "find duplicate products".
   - **inventory** — Track and reconcile what's in stock. Sample asks: "what's running low?", "do a cycle count of my warehouse", "I just received a shipment".
   - **orders** — Read-only views of orders. Sample asks: "yesterday's orders", "what's stuck unfulfilled?", "find an order from Customer A".
   - **customers** — Read-only customer cohorts and lists. Sample asks: "show me my VIPs", "who's my top spender?", "audit marketing consent".
   - **promotions** — Create and audit discounts. Sample asks: "create a 10% sale", "what discounts are running?", "discount the Summer collection".
   - **analytics** — Sales, products, referrers, funnel, cohorts. Sample asks: "today's sales", "best and worst sellers", "where are my customers coming from?".
   - **retention** — Win-back and anniversary cohorts. Sample asks: "list lapsed customers", "who's near their first-order anniversary?".
   - **storefront** — Shop snapshot and new-store previews. Sample asks: "what plan am I on?", "preview a new store from a description".
   - **reporting** — End-of-week brief. Sample asks: "send me a weekly brief".

3. **End with a CTA**, depending on `onboarding_state`:
   - If `needs_onboarding: true` (no profile): "Want to set up properly? Say 'set me up'."
   - If `onboarding_skipped: true`: "You skipped onboarding earlier — that's fine. Say 'set me up' any time."
   - Otherwise (profile present): "All set up. What do you want to work on?"

4. **NO writes.** This is purely informational. Never call any mutation, never confirm, never write.

## Output format

Plain markdown-style text. Bold the category name; one-line description; bullet sample asks. Around 9 short blocks. Keep total length skimmable — under one screenful.

## Edge cases

- **No `onboarding_state` yet.** Tour still works fine. CTA points at `onboarding.first-run`.
- **switch-shop mid-tour.** No state to lose; just re-run after switch.
- **Plan-tier cap on a category.** If a particular skill is gated (e.g. cohort retention requires Shopify+), include the category in the tour but flag the limit inline ("requires Shopify plan or higher").

## Examples

- [`examples/basic.md`](examples/basic.md)
