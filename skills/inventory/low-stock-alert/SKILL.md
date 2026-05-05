---
id: inventory.low-stock-alert
name: low-stock-alert
title: Low-stock alert
description: Find products below a stock threshold — global, per-cohort, or with a custom threshold per product type.
audience: merchant
roles: [operations, merchandiser, founder]
category: inventory
risk: read
mcp_tools:
  - get-shop-info
  - search_products
  - get-inventory-levels
graphql:
  query: false
  mutation: false
triggers:
  - "low stock"
  - "what's running low"
  - "products almost out"
  - "low-stock alert"
  - "what do I need to reorder"
inputs:
  - name: threshold
    type: integer
    default: 5
    description: Total inventory at or below this triggers alert.
  - name: cohort
    type: string
    default: "all"
  - name: include_out_of_stock
    type: boolean
    default: false
    description: Include products with 0 stock too. Default skips them — they're already past low.
outputs:
  summary: |
    Ranked list (lowest first) of products at/below threshold, with current total, locations, and recent sales.
status: stable
version: 1.0.0
---

## Workflow

1. Cohort + threshold via `_system/cohort-builder`. Add filter `inventory_total:<=<threshold>` (and `inventory_total:>0` if `include_out_of_stock` is false).
2. `search_products` → paginated read.
3. For each, fetch recent sales:
   ```
   FROM sales SHOW orders WHERE product_id = '<gid>' SINCE -30d UNTIL today
   ```
   This sets priority — running out of slow movers is fine; running out of fast movers is a fire.
4. Render ranked by (recent sales desc, current stock asc).

## Output format

```
example-shop · low stock alert · threshold 5

   Product       Stock   30d sales   Note
   ──────────────────────────────────────
   Product A        2          18    ⚠ fast mover, reorder now
   Product B        4          12    reorder soon
   Product C        1           8
   Product D        3           4
   Product E        5           1    slow — fine to wait

Want to plan a reorder? Ask "draft a reorder list for these".
                       (Note: actual reorder PO needs your supplier/3PL —
                        not in this skill bundle. See meta.wiring.)
```

## Edge cases

- **Untracked products.** Excluded.
- **Sales lookup failures.** Don't drop the product; show "—" for sales and put it last.
- **Threshold 0.** Equivalent to "completely out" — point them at `inventory.snapshot include_zero: true`.

## Examples

- [`examples/basic.md`](examples/basic.md)
