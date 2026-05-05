---
id: promotions.collection-sale
name: collection-sale
title: Plan a collection sale
description: End-to-end "set up a sale on this collection" — pick the collection, pick the dates, pick the percentage, plus optional minimums; preview, confirm, create.
audience: merchant
roles: [cmo, founder]
category: promotions
risk: write
mcp_tools:
  - get-shop-info
  - search_collections
  - get-collection
  - create-discount
graphql:
  query: false
  mutation: false
triggers:
  - "run a sale on [collection]"
  - "set up a collection sale"
  - "discount the [X] collection"
  - "X% off the [Y] collection"
  - "schedule a sale"
inputs:
  - name: collection
    type: string
    description: Collection name or GID.
  - name: percentage
    type: number
  - name: code
    type: string
    description: Discount code customers will type. If omitted, suggest one.
  - name: starts_at
    type: string
  - name: ends_at
    type: string
  - name: minimum_purchase
    type: number
  - name: minimum_quantity
    type: integer
outputs:
  summary: |
    Sale brief: collection (with product count), percentage, code, validity, minimums. Plus the
    estimated catalog revenue at risk.
safety:
  blocks: []
  requires_confirm: true
  rollback: |
    Same as promotions.discount-create — set endsAt to now via GraphQL discountCodeBasicUpdate
    to deactivate immediately. Inverse op included in run log.
status: stable
version: 1.0.0
---

## Workflow

This is a friendlier wrapper around `promotions.discount-create`, scoped to collections, with a sanity-check on revenue at risk.

1. Resolve `collection` to GID. If multiple matches, ask. If 0 matches, suggest closest.
2. Read collection: count of products, recent sales (`run-analytics-query` `FROM sales SHOW total_sales WHERE collection_id = '<gid>' SINCE -30d`).
3. Validate dates (`starts_at` < `ends_at`; both ≥ now or warn).
4. If `code` not provided, suggest one based on the collection handle (e.g., `SPRING2026-20`).
5. Compose the brief — including a sanity-check line on revenue at risk:
   ```
   Estimate: at 20% off across this collection's recent sales pace ($X,XXX/mo),
             roughly $XXX in margin will be discounted across the sale window.
   ```
6. Confirm.
7. Call `create-discount` with `collectionId` set.
8. Log + provide GraphQL deactivation operation.

## Safety

- This is a more cautious wrapper than `discount-create` — it surfaces revenue context so the merchant doesn't accidentally launch a 50% off sale on the highest-margin collection.
- High percentages (≥ 50%) get an extra check, same as `discount-create`.
- Past `starts_at` triggers a confirmation: "This will be active immediately. Confirm?"

## Edge cases

- **Collection has 0 products.** Refuse — pointless sale.
- **Collection has 0 sales last 30 days.** Surface — sale won't move what isn't already selling. Don't refuse; merchant may be running it for clearance.
- **Multiple collections match.** Ask which.

## Examples

- [`examples/basic.md`](examples/basic.md)
