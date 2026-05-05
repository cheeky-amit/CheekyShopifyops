---
id: customers.high-value-list
name: high-value-list
title: Top customers by spend
description: List top spenders or repeat buyers, with lifetime spend and order count, abbreviated for privacy.
audience: merchant
roles: [cmo, founder, cx]
category: customers
risk: read
mcp_tools:
  - get-shop-info
  - list-customers
graphql:
  query: false
  mutation: false
triggers:
  - "top customers"
  - "top spenders"
  - "best customers"
  - "repeat buyers"
  - "highest-spending customers"
inputs:
  - name: kind
    type: string
    default: "by-spend"
    description: "by-spend | by-orders"
  - name: limit
    type: integer
    default: 20
  - name: cohort
    type: string
    default: "all"
    description: "Optional cohort to filter within (e.g. 'subscribers', 'lapsed-vips')."
outputs:
  summary: |
    Ranked list with first name + last initial, lifetime spend, order count, country.
status: stable
version: 1.0.0
---

## Workflow

1. Resolve cohort via `primitives/cohort-builder`.
2. `list-customers` with cohort filter, sorted by `total_spent` desc (or `orders_count` desc).
3. Render abbreviated.

## Output format

```
example-shop · top 20 by spend

   1. Customer A B.   $X,XXX.XX   23 orders   US
   2. Customer C D.   $X,XXX.XX   18 orders   CA
   …

Want to see one in full? "show details for #1" or "show Customer A B."
```

## Edge cases

- **Privacy.** Always abbreviate. Full names + emails appear only when the merchant asks for one specific customer.
- **Empty result.** "No customers match." Suggest cohort tweaks.
- **Tied spend.** Stable sort by customer ID secondary.

## Examples

- [`examples/basic.md`](examples/basic.md)
