---
id: orders.fulfillment-watchlist
name: fulfillment-watchlist
title: Stuck unfulfilled orders
description: Surface paid-but-unfulfilled orders sorted by age, with the oldest first, so nothing rots in the queue.
audience: merchant
roles: [operations, cx, founder]
category: orders
risk: read
mcp_tools:
  - get-shop-info
  - list-orders
graphql:
  query: false
  mutation: false
triggers:
  - "show stuck orders"
  - "what orders haven't shipped"
  - "fulfillment watchlist"
  - "orders that need attention"
  - "what hasn't shipped yet"
inputs:
  - name: stale_after_hours
    type: integer
    default: 72
    description: An order is "stale" if it's been paid-but-unfulfilled longer than this. Default 72 hours.
  - name: include_partial
    type: boolean
    default: true
    description: Include partially fulfilled orders too.
outputs:
  summary: |
    Grouped list: stale (older than threshold), today, partial. Each row shows age, order name,
    customer (first name + last initial), total, line item count.
status: stable
version: 1.0.0
---

## Purpose

Catch orders that are slipping through the cracks. The merchant doesn't want a CSV — they want "here's what's late and who's waiting."

## When to use

- Daily ops review.
- Before responding to "where's my order?" customer emails.

## When NOT to use

- The merchant wants today's full order picture. Use `orders.daily-pulse`.
- The merchant wants a specific order. Use `orders.search`.

## Workflow

1. Compose cohort via `primitives/cohort-builder` with named cohort `at-risk-stuck` (= `fulfillment_status:unfulfilled AND created_at:<'<stale_after_hours-ago>'`) plus `paid-unfulfilled` for the today bucket. If `include_partial`, also pull `fulfillment_status:partial`.
2. Read each cohort via `list-orders` with `query` filter, paginated via `primitives/data-extraction` (cap 100 — beyond that, the merchant should triage by date).
3. For each order, compute age in hours.
4. Sort by age desc within each bucket.
5. Render grouped list. Strip customer last name to first initial for display ("Customer A B." not full surname) — privacy-respectful summary; merchant can ask for one specific order's details.

## Output format

```
example-shop · stuck orders

→ STALE  (paid, unfulfilled, > 72h)        4
  • ORDER-1001  92h   Customer A B.   $XXX.XX   3 items
  • ORDER-1002  84h   Customer C D.   $XX.XX    1 item
  • ORDER-1003  78h   Customer E F.   $XXX.XX   2 items
  • ORDER-1004  74h   Customer G H.   $XX.XX    1 item

→ TODAY  (paid, unfulfilled, < 72h)        8
  • ORDER-1005  3h    …
  …

→ PARTIAL                                   1
  • ORDER-1006  …

To see one order in detail: "show ORDER-1001".
```

## Edge cases

- **Zero stuck orders.** Celebrate it: "Nothing stuck. 🎉" — first time we explicitly use an emoji; merchants like positive feedback. (No emoji elsewhere.)
- **>100 stale orders.** Indicates a bigger problem. Surface: "More than 100 paid orders are unfulfilled past 72h. This is bigger than a watchlist — let's set up a triage."
- **Cancelled orders mixed in.** `list-orders` returns cancelled orders too. Filter by financial_status; cancelled orders are not on the watchlist.

## Examples

- [`examples/basic.md`](examples/basic.md)
