---
id: analytics.cohort-retention
name: cohort-retention
title: New vs returning customer mix
description: Show the share of orders from new vs returning customers over time, plus returning-customer rate trend.
audience: merchant
roles: [cmo, founder, cfo]
category: analytics
risk: read
mcp_tools:
  - get-shop-info
  - run-analytics-query
graphql:
  query: false
  mutation: false
triggers:
  - "new vs returning customers"
  - "customer retention"
  - "returning customer rate"
  - "cohort retention"
  - "first-time vs repeat orders"
inputs:
  - name: range
    type: string
    default: "last-90d"
  - name: granularity
    type: string
    default: "week"
    description: "day | week | month"
outputs:
  summary: |
    Time-series chart of new vs returning. Plus headline: % of orders from returning customers.
status: stable
version: 1.0.0
---

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity, `shop.timezone` for range resolution, and `shop.plan` to capability-check (this skill needs Shopify or higher; on Basic, surface the limit cleanly).
1. Run the queries:

```
FROM sales SHOW returning_customers, customers, returning_customer_rate
TIMESERIES <granularity> SINCE <s> UNTIL <u>
COMPARE TO previous_period
```

Then a complementary:

```
FROM customers SHOW new_customers, returning_customers
TIMESERIES <granularity> SINCE <s> UNTIL <u>
```

Render combined chart + headline rate.

## Output format

```
example-shop · new vs returning customers · last 90 days

  Returning rate: 34%   (+4pp vs prior 90d)
  This week:      36%

  New customers (last 90d):       420
  Returning customers (last 90d): 218

[time-series chart of new (dotted) vs returning (solid)]

Trend:  returning rate has crept up 4pp over the period — repeat purchase
        engine is improving.
```

## Edge cases

- **Cold-start stores.** If the shop is <90 days old, returning rate is unstable. Surface that.
- **Very high returning %.** If returning > 70%, ask the merchant if they're running a subscription program — that skews the metric.

## Examples

- [`examples/basic.md`](examples/basic.md)
