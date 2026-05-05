---
id: customers.subscriber-audit
name: subscriber-audit
title: Email marketing consent audit
description: Audit who's subscribed, pending, not subscribed, invalid, or redacted — across the full base or a cohort — for clean lists and compliance hygiene.
audience: merchant
roles: [cmo, compliance, founder]
category: customers
risk: read
mcp_tools:
  - get-shop-info
  - list-customers
graphql:
  query: false
  mutation: false
triggers:
  - "audit my email subscribers"
  - "marketing consent audit"
  - "who's subscribed to email"
  - "what's my consent rate"
  - "any invalid emails"
inputs:
  - name: cohort
    type: string
    default: "all"
outputs:
  summary: |
    Counts by email_marketing_state: subscribed, pending, not_subscribed, invalid, redacted.
    Plus: subscribed share, invalid count (action needed), unsubscribe-rate-of-orders proxy.
status: stable
version: 1.0.0
---

## Purpose

Compliance hygiene. Knowing who's actually subscribed, who's invalid, and what % of orders convert to subscribers is a quarterly merchant ask.

## Workflow

1. Resolve cohort.
2. `list-customers` paginated, group by `email_marketing_state`.
3. Compute counts and shares.

## Output format

```
example-shop · subscriber audit · cohort: all

  Total customers:    2,140

  Subscribed:         1,520 (71%)
  Pending (DOI):         28 (1%)
  Not subscribed:       540 (25%)
  Invalid:               42 (2%)        ← needs cleanup
  Redacted (GDPR):       10 (0.5%)

  Subscribed customers with ≥1 order:   1,201
  Orders → Subscriber conversion:       69%

To clean invalid emails: those need re-collection or deletion.
This skill won't auto-update consent — that requires a separate
write skill (not in v1, but doable via GraphQL).
```

## Edge cases

- **Redacted (GDPR).** Always surface separately — these are protected.
- **Pending.** Double-opt-in awaiting confirmation. Note that some pending will time out.
- **Invalid.** Bounced or formatted wrong. Surface as a number — actionable but the merchant fixes outside this skill.

## Examples

- [`examples/basic.md`](examples/basic.md)
