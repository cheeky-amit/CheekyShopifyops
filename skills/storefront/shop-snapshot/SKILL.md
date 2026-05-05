---
id: storefront.shop-snapshot
name: shop-snapshot
title: Shop snapshot
description: Plan tier, currency, timezone, country, plus a few high-level catalog/inventory/order counts — a one-glance "where am I" for the connected store.
audience: merchant
roles: [founder, operations]
category: storefront
risk: read
mcp_tools:
  - get-shop-info
  - search_products
  - search_collections
  - list-orders
  - run-analytics-query
graphql:
  query: false
  mutation: false
triggers:
  - "shop snapshot"
  - "store info"
  - "what plan am I on"
  - "what's my store status"
  - "show me my store"
inputs: []
outputs:
  summary: |
    Single-screen summary: shop profile + counts (products, collections, orders last 7d, total sales last 7d).
status: stable
version: 1.0.0
---

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity. shop-context already returns the basic profile, so step 1 below uses that instead of a fresh `get-shop-info` call.
1. Use `shop` from context for profile (skip the redundant `get-shop-info` call).
2. `search_products` with `first: 1` to read `pageInfo` (no — Shopify doesn't return total count via this; instead use `run-analytics-query`):
   ```
   FROM products SHOW count
   ```
3. Same for collections (no analytics surface — fall back to a paginated `search_collections` count or skip with a "tap to load" pointer).
4. `run-analytics-query`:
   ```
   FROM sales SHOW orders, total_sales SINCE -7d UNTIL today
   ```
5. Compose snapshot.

## Output format

```
example-shop

  Domain:        example-shop.myshopify.com
  Plan:          Shopify
  Currency:      USD
  Timezone:      America/Denver
  Country:       United States

  Catalog:       248 products · 18 collections
  Last 7 days:   84 orders · $9,820 sales

What now?
  • Today's pulse:        "pulse"
  • Stuck orders:         "stuck"
  • Top sellers:          "top products"
```

## Edge cases

- **Counts unavailable on plan tier.** Fall back to a "tap to load" message or omit.
- **Multi-currency.** The single-currency line is shop-default; mention if Markets is enabled.

## Examples

- [`examples/basic.md`](examples/basic.md)
