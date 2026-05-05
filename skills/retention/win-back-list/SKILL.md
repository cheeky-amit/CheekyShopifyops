---
id: retention.win-back-list
name: win-back-list
title: Build a win-back list
description: Identify lapsed customers (last order > N months ago, ≥ X past orders) — for the merchant to feed into a marketing send.
audience: merchant
roles: [cmo, founder]
category: retention
risk: read
mcp_tools:
  - get-shop-info
  - list-customers
graphql:
  query: false
  mutation: false
triggers:
  - "win-back list"
  - "lapsed customers"
  - "customers we should win back"
  - "build a re-engagement list"
inputs:
  - name: lapsed_months
    type: integer
    default: 6
  - name: min_past_orders
    type: integer
    default: 2
    description: Customer must have placed at least this many orders to make the list (filters churn from one-time buyers).
  - name: subscribed_only
    type: boolean
    default: true
    description: Only include subscribed customers (you can email them legally).
outputs:
  summary: |
    Cohort snapshot + ranked list (highest lifetime spend first), abbreviated names, ready
    to feed into a marketing send.
status: stable
version: 1.0.0
---

## Workflow

1. Build cohort via `primitives/cohort-builder` (named cohort `lapsed` with custom thresholds; AND `email_marketing_state:subscribed` if `subscribed_only`).
2. Read via `primitives/data-extraction` (cap 500).
3. Sort by `total_spent` desc.
4. Render snapshot + abbreviated list (first name + last initial).

## Output format

```
example-shop · win-back list · lapsed >6 months, ≥2 past orders, subscribed

Cohort summary:
  Count:           86
  Avg lifetime:    $XXX.XX
  Avg orders:      3.4
  Avg time since:  9.2 months

Ranked (top 30 shown):
   1.  Customer A B.    $XXX.XX    5 orders   last:  2025-04-12
   2.  Customer C D.    $XXX.XX    4 orders   last:  2025-05-22
   …

To send to these customers: this skill doesn't send email or SMS — that needs
your email/SMS MCP. See meta.wiring for combining with email tools.

Want a CSV-like dump? Ask "export win-back list" — I'll format with full names
and emails (privileged view, ask explicitly).
```

## Edge cases

- **Empty cohort.** "No customers fit. Either you don't have lapsed-multi-buyer customers (good problem to have) or your data is too young."
- **Privacy.** Default view abbreviates names. Full names + emails appear only when the merchant explicitly asks for the export view.
- **`subscribed_only: false`.** Surface a privacy note: "Including unsubscribed customers — you can't legally email them; this list is for analysis only."

## Examples

- [`examples/basic.md`](examples/basic.md)
