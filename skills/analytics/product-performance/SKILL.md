---
id: analytics.product-performance
name: product-performance
title: Top and bottom product performers
description: Rank products by gross sales, orders, or sell-through rate over a date range — surface what's working and what isn't.
audience: merchant
roles: [merchandiser, founder, cmo]
category: analytics
risk: read
mcp_tools:
  - get-shop-info
  - run-analytics-query
graphql:
  query: false
  mutation: false
triggers:
  - "top products"
  - "bottom products"
  - "best sellers"
  - "what's not selling"
  - "product performance"
  - "sell-through"
inputs:
  - name: range
    type: string
    default: "last-30d"
  - name: rank_by
    type: string
    default: "gross-sales"
    description: "gross-sales | orders | sell-through-rate | net-sales"
  - name: limit
    type: integer
    default: 10
  - name: direction
    type: string
    default: "top"
    description: "top | bottom"
outputs:
  summary: |
    Ranked product list with the chosen metric, plus secondary metrics (orders, gross sales,
    sell-through if available).
status: stable
version: 1.0.0
---

## Workflow

1. Resolve range and rank_by.
2. For sales-based ranks:
   ```
   FROM sales SHOW gross_sales, net_sales, orders
   GROUP BY product_title
   SINCE <s> UNTIL <u>
   ORDER BY <rank_by> [DESC|ASC]
   LIMIT <limit>
   ```
3. For sell-through:
   ```
   FROM inventory SHOW starting_inventory_units, ending_inventory_units, inventory_units_sold, sell_through_rate
   GROUP BY product_title
   SINCE <s> UNTIL <u>
   ORDER BY sell_through_rate [DESC|ASC]
   LIMIT <limit>
   ```
4. Render ranked list.

## Output format

```
example-shop · top 10 products by gross sales · last 30 days

   1.  Product A    $X,XXX   24 orders   $XX AOV
   2.  Product B    $X,XXX   18 orders   $XX
   …

To see one in detail: ask "show Product A".
To compare with bottom: ask "bottom 10 products".
```

## Edge cases

- **Tie-breaking.** Stable secondary sort by orders, then alphabetical.
- **No data in range.** "No sales in <range>." Don't show an empty ranked list.
- **Sell-through requires inventory tracking.** If the merchant has many untracked products, sell-through rates will be misleading. Surface a footnote.

## Examples

- [`examples/basic.md`](examples/basic.md)
