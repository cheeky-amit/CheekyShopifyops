---
id: analytics.daily-pulse
name: daily-pulse
title: Daily sales pulse over a date range
description: Sales, orders, AOV, sessions, and conversion as a time series with comparison to the previous period — for any date range.
audience: merchant
roles: [founder, cfo, cmo]
category: analytics
risk: read
mcp_tools:
  - get-shop-info
  - run-analytics-query
graphql:
  query: false
  mutation: false
triggers:
  - "weekly sales"
  - "monthly sales"
  - "sales last 30 days"
  - "sales for [date range]"
  - "sales pulse"
inputs:
  - name: range
    type: string
    default: "last-7d"
    description: "today | yesterday | last-7d | last-30d | last-90d | mtd | qtd | ytd | <SINCE>..<UNTIL>"
  - name: compare
    type: boolean
    default: true
    description: Compare to previous equivalent period.
outputs:
  summary: |
    Time-series chart + totals: orders, total_sales, AOV, sessions, conversion. Plus deltas vs previous period.
status: stable
version: 1.0.0
---

## Workflow

1. Resolve range to ShopifyQL `SINCE` / `UNTIL` values using shop timezone.
2. Run two queries:
   - Sales: `FROM sales SHOW orders, total_sales, average_order_value TIMESERIES day SINCE <s> UNTIL <u>` + `COMPARE TO previous_period` if `compare`.
   - Sessions: `FROM sessions SHOW sessions, conversion_rate TIMESERIES day SINCE <s> UNTIL <u>` + compare.
3. Combine: render a single summary with chart (the run-analytics-query widget handles charting) plus headline totals.

## Output format

```
example-shop · last 7 days (2026-01-09 to 2026-01-15)

  Orders:        84       (+12% vs prior 7d)
  Total sales:   $X,XXX   (+8%)
  AOV:           $XX      (-3%)
  Sessions:      4,210    (+22%)
  Conversion:    2.0%     (-0.4 pp)

[time-series widget rendered by the host]
```

## Edge cases

- **Plan-tier.** Some session metrics need Shopify or higher. Fall back to sales-only if blocked.
- **Multi-currency stores.** `total_sales` is in shop currency. Surface that.
- **Custom range parsing.** Accept `2026-01-01..2026-01-15` shorthand. Reject reversed (UNTIL before SINCE) with clear error.

## Examples

- [`examples/basic.md`](examples/basic.md)
