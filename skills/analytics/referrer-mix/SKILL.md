---
id: analytics.referrer-mix
name: referrer-mix
title: Where do sales come from
description: Break down sessions and orders by referrer source (direct, social, search, email, paid) and named referrer for any range.
audience: merchant
roles: [cmo, founder]
category: analytics
risk: read
mcp_tools:
  - get-shop-info
  - run-analytics-query
graphql:
  query: false
  mutation: false
triggers:
  - "where do sales come from"
  - "referrer breakdown"
  - "traffic sources"
  - "what's driving sales"
  - "marketing channel mix"
inputs:
  - name: range
    type: string
    default: "last-30d"
  - name: dimension
    type: string
    default: "source"
    description: "source | source-and-name (drill into Instagram vs Facebook within social, etc.)"
outputs:
  summary: |
    Ranked rows by orders + total_sales, grouped by referrer dimension. Plus sessions for each.
status: stable
version: 1.0.0
---

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity, `shop.timezone` for range resolution, and `store.stage` to scale display caps (default 20 rows; up to 50 on `1m+`).
1. Run two queries (then merge in render):
   ```
   FROM sales SHOW orders, total_sales
   GROUP BY order_referrer_source [, order_referrer_name]
   SINCE <s> UNTIL <u>

   FROM sessions SHOW sessions
   GROUP BY referrer_source [, referrer_name]
   SINCE <s> UNTIL <u>
   ```
2. Join by source (and optional name). Compute conversion rate per row.
3. Render ranked.

## Output format

```
example-shop · referrer mix · last 30 days

  Source              Sessions    Orders    Sales     Conv
  ────────────────────────────────────────────────────────
  direct                1,820        58    $X,XXX    3.2%
  search                1,420        42    $X,XXX    3.0%
  social                  920        24    $X,XXX    2.6%
  email                   320        18    $X,XXX    5.6%
  paid                    340        12    $X,XXX    3.5%

Drill in:  ask "referrer mix by name last 30 days".
```

## Edge cases

- **Long tail.** Cap displayed rows at 20; group the rest into "other".
- **No sessions data.** Some plans don't expose sessions. Fall back to orders-only and label it.
- **Direct = unknown.** Most "direct" is actually unattributable. Note this.

## Examples

- [`examples/basic.md`](examples/basic.md)
