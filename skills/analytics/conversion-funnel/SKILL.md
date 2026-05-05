---
id: analytics.conversion-funnel
name: conversion-funnel
title: Session-to-purchase funnel
description: Sessions → cart adds → reached checkout → completed checkout, with drop-off at each stage.
audience: merchant
roles: [cmo, founder, merchandiser]
category: analytics
risk: read
mcp_tools:
  - get-shop-info
  - run-analytics-query
graphql:
  query: false
  mutation: false
triggers:
  - "conversion funnel"
  - "where are people dropping off"
  - "checkout funnel"
  - "session funnel"
  - "cart abandonment"
inputs:
  - name: range
    type: string
    default: "last-30d"
  - name: split_by
    type: string
    default: "none"
    description: "none | device-type | country | referrer-source"
outputs:
  summary: |
    Five-row funnel with counts and conversion rate at each step. Drop-off rate per step.
    Optional split.
status: stable
version: 1.0.0
---

## Workflow

```
FROM sessions SHOW sessions, sessions_with_cart_additions,
                  sessions_that_reached_checkout, sessions_that_completed_checkout, conversion_rate
[GROUP BY session_device_type | session_country | referrer_source]
SINCE <s> UNTIL <u>
```

If `split_by`, render one funnel per group; else single funnel.

## Output format

```
example-shop · conversion funnel · last 30 days

                          Sessions    Step rate    Cum rate
  Sessions                   4,210
  → cart add                   840    20%          20%
  → reached checkout           420    50%          10%
  → completed checkout         210    50%          5%

Overall conversion: 5.0%
Drop-off pattern: half drop off cart→checkout, half checkout→complete.
                  Worth investigating shipping or payment friction.
```

## Edge cases

- **Plan-tier.** Sessions analytics requires Shopify or higher.
- **Bot traffic.** Inflates sessions. Surface a note if conversion looks oddly low (<0.5%).

## Examples

- [`examples/basic.md`](examples/basic.md)
