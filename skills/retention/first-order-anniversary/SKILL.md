---
id: retention.first-order-anniversary
name: first-order-anniversary
title: First-order anniversary cohort
description: Find customers approaching their first-order anniversary (1y or other window) — the natural moment for a "thank you / come back" touch.
audience: merchant
roles: [cmo, cx]
category: retention
risk: read
mcp_tools:
  - get-shop-info
  - list-customers
graphql:
  query: true
  mutation: false
triggers:
  - "first-order anniversary"
  - "customers approaching one year"
  - "anniversary cohort"
  - "who's near their birthday with us"
inputs:
  - name: window_days
    type: integer
    default: 14
    description: Anniversary +/- this many days.
  - name: years
    type: integer
    default: 1
    description: 1 for one-year anniversary; 2 for two-year, etc.
  - name: subscribed_only
    type: boolean
    default: true
outputs:
  summary: |
    Ranked list of customers whose first order falls inside the anniversary window. Counts +
    sample names abbreviated.
status: stable
version: 1.0.0
---

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity, `shop.timezone` for "today" anchoring, and `store.stage` to scale pagination (cap at 500 by default; up to 2000 on `1m+`).
1. Build the GraphQL query.

`list-customers` doesn't expose first-order date as a filter field directly. Use GraphQL via `_system/graphql-helper`:

```
query AnniversaryCohort($after: String) {
  customers(first: 50, after: $after, query: "<email_marketing_state:subscribed>") {
    edges { node {
      id
      firstName
      lastName
      email
      numberOfOrders
      amountSpent { amount currencyCode }
      orders(first: 1, sortKey: PROCESSED_AT) {
        edges { node { processedAt } }
      }
    } }
    pageInfo { hasNextPage endCursor }
  }
}
```

Then filter in-skill: keep customers whose oldest order's `processedAt` is within `years*365 ± window_days` days of today.

## Output format

```
example-shop · first-order anniversary cohort · 1y ± 14d

Count:    24

  •  Customer A B.   first order 2025-01-15   $84.00     1 order
  •  Customer C D.   first order 2025-01-09   $128.00    2 orders
  •  Customer E F.   first order 2025-01-22   $54.00     1 order
  …  (21 more)

Send a "happy 1 year with us" email — needs your email MCP (not in this bundle).
```

## Edge cases

- **Cohort smaller than expected.** Anniversary cohorts are naturally small (single-day window). Wider `window_days` for sparse stores.
- **Non-marketing-eligible customers.** With `subscribed_only` true, unsubscribed customers are filtered. Surface count of filtered.
- **Multi-year.** `years: 2` finds 2-year anniversaries; the skill labels accordingly.

## Examples

- [`examples/basic.md`](examples/basic.md)
