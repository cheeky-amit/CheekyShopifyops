---
id: reporting.weekly-merchant-brief
name: weekly-merchant-brief
title: Weekly merchant brief
description: One end-of-week summary covering sales, top movers, low stock, stuck orders, anomalies — the digest the merchant reads on Monday morning.
audience: merchant
roles: [founder, operations]
category: reporting
risk: read
mcp_tools:
  - get-shop-info
  - run-analytics-query
  - search_products
  - get-inventory-levels
  - list-orders
graphql:
  query: false
  mutation: false
triggers:
  - "weekly brief"
  - "weekly summary"
  - "Monday brief"
  - "what happened last week"
  - "weekly merchant report"
inputs:
  - name: week
    type: string
    default: "last-7d"
    description: "last-7d | this-week | last-week"
outputs:
  summary: |
    Five sections: headline numbers, top movers (up and down), low stock alert, stuck orders,
    anomalies/things to look at this week.
status: stable
version: 1.0.0
---

## Purpose

A composite skill: it doesn't introduce new MCP calls but orchestrates several primitives + read-mostly skills into one digestible Monday-morning brief.

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity. Use `rituals.monday_brief` and `rituals.weekly_brief_day` to decide whether to greet with "your weekly brief" framing. Use `store.stage` to scale: top/bottom-N defaults to 5 (consider 10 on `1m+`); low-stock threshold defaults via the inventory skill (5 / 10 / 25 by stage).

1. Compose:

- `analytics.daily-pulse` (range = week) — sales + sessions + conversion + comparison.
- `analytics.product-performance` (top 5 by gross sales, bottom 5 by gross sales).
- `inventory.low-stock-alert` (threshold default — fast movers prioritized).
- `orders.fulfillment-watchlist` (paid + unfulfilled, ranked by age).
- `analytics.cohort-retention` (returning rate, just the headline).

Then render one combined view, no widgets duplicated.

## Output format

```
example-shop · weekly brief · 2026-01-08 → 2026-01-15

HEADLINE
  Orders:        84       (+12% vs prior 7d)
  Total sales:   $9,820   (+8%)
  AOV:           $117     (-3%)
  Sessions:      4,210    (+22%)
  Conversion:    2.0%     (-0.4 pp)
  Returning:     34%      (+4 pp)

TOP MOVERS (top 5 by sales)
  1.  Product A    $1,820   24 orders
  2.  Product B    $1,210   18 orders
  3.  Product C    $940     12 orders
  4.  Product D    $720     8 orders
  5.  Product E    $580     6 orders

UNDER-PERFORMERS (bottom 5)
  Product P    $0    0 orders   — flag for status audit
  Product Q    $0    0 orders   — out of stock since Sep
  Product R    $40   1 order
  Product S    $80   2 orders
  Product T    $120  3 orders

LOW STOCK (≤5 with recent sales)
  Product A    2    18 sold last 30d   ⚠ reorder now
  Product B    4    12 sold
  Product C    1    8 sold

STUCK ORDERS (paid + unfulfilled > 72h)
  ORDER-1001   92h
  ORDER-1002   84h
  ORDER-1003   78h
  ORDER-1004   74h

ANOMALIES / WATCH
  • AOV down 3% while session count up 22% → more browsers, smaller carts.
    Worth checking the funnel: ask "conversion funnel last 7 days".
  • Returning rate +4pp — repeat-purchase engine improving.
  • Product Q listed as ACTIVE but no stock since Sep 2025 → run product-status-audit.
```

## Edge cases

- **Plan-tier blocks one of the underlying queries.** The brief still renders without that section, with a one-line note.
- **Low-volume store.** If orders < 10 for the week, suppress the funnel/conversion line — too noisy. Show simple counts instead.

## Examples

- [`examples/basic.md`](examples/basic.md)
