---
id: orders.daily-pulse
name: daily-pulse
title: Today's order pulse
description: One-glance summary of today's and yesterday's orders — count, AOV, fulfillment status, total sales — with day-over-day change.
audience: merchant
roles: [founder, operations, cx]
category: orders
risk: read
mcp_tools:
  - get-shop-info
  - list-orders
  - run-analytics-query
graphql:
  query: false
  mutation: false
triggers:
  - "today's orders"
  - "how are sales today"
  - "give me a pulse on orders"
  - "what happened yesterday"
  - "daily order summary"
inputs:
  - name: window
    type: string
    default: "today"
    description: "today | yesterday | today-vs-yesterday"
outputs:
  summary: |
    Header line with shop pretty + window.
    Numbers: order count, total sales, AOV, paid orders, unfulfilled orders.
    Day-over-day delta if window is comparison mode.
status: stable
version: 1.0.0
---

## Purpose

A quick health-check a merchant can ask any time. "How are sales today?" → 6-line summary, not a CSV.

## When to use

- Morning standup, end-of-day review.
- Anytime a merchant wants context before diving deeper.

## When NOT to use

- The merchant wants a specific date range. Use `analytics.daily-pulse` (which supports SINCE/UNTIL and TIMESERIES).
- The merchant wants to find a specific order. Use `orders.search`.

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes:
   - `shop`, `operator`, `store`, `rituals`, `onboarding_state`.
   - `needs_onboarding` and `onboarding_skipped` flags.

   If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity, `operator.write_defaults` for confirm strictness, and `store.stage` to scale defaults. Reuse `shop.timezone` in step 1 for computing "today" (matters when crossing a date boundary).

1. For sales/AOV/orders, call `run-analytics-query`:
   ```
   FROM sales
   SHOW orders, total_sales, average_order_value
   SINCE today UNTIL today
   ```
   For `today-vs-yesterday`, append `COMPARE TO previous_period`.
2. For fulfillment status, call `list-orders` with `query: "created_at:>'<today-start>'"` and bucket by `fulfillment_status` (`unfulfilled`, `partial`, `fulfilled`).
3. Compose summary.

## Output format

```
example-shop · today (2026-01-15)

  Orders:        12        (+3 vs yesterday)
  Total sales:   $X,XXX.XX (+$XXX.XX vs yesterday)
  AOV:           $XX.XX
  Paid:          11
  Unfulfilled:   8         (oldest: 2 hours)
```

## Edge cases

- **Zero orders today.** Surface clearly: "No orders yet today." Don't show "+0" deltas.
- **Timezone boundary.** "Today" is the shop's timezone, not the merchant's. Mention the timezone in the header.
- **Plan-tier analytics.** Some `run-analytics-query` aggregates require Shopify or higher plan. If the call fails on plan limits, fall back to `list-orders` with `created_at` filter and compute count/sum locally.

## Examples

- [`examples/basic.md`](examples/basic.md)
