---
id: inventory.snapshot
name: snapshot
title: Inventory snapshot
description: Pull current stock for a product, a cohort, or the whole catalog — across all locations — with totals and per-location breakdown.
audience: merchant
roles: [operations, merchandiser, founder]
category: inventory
risk: read
mcp_tools:
  - get-shop-info
  - search_products
  - get-product
  - get-inventory-levels
graphql:
  query: false
  mutation: false
triggers:
  - "inventory snapshot"
  - "what's my stock"
  - "stock levels for [product/cohort]"
  - "show inventory across locations"
  - "how much do I have of X"
inputs:
  - name: target
    type: string
    description: |
      One of:
        a single product ("Product A", a SKU, or a GID)
        a cohort ("vendor:Vendor A", "tag:summer", or "all")
  - name: include_zero
    type: boolean
    default: false
    description: Include products with 0 total stock.
outputs:
  summary: |
    For one product: per-variant × per-location grid.
    For a cohort: ranked list with totals and "out at any location" flag.
status: stable
version: 1.0.0
---

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity, `operator.write_defaults` for confirm strictness, and `store.stage` to scale defaults — for cohort snapshots, scale the 200-product cap up to ~500 on `100k-1m`/`1m+` stores.
1. Resolve target. If single product (handle, SKU, name match), call `get-product` then `get-inventory-levels`.
2. If cohort, build via `_system/cohort-builder`, paginate via `_system/data-extraction`, then for each product call `get-inventory-levels`.
3. Render. For single product: variant × location grid. For cohort: per-product totals.

## Output format

Single product:

```
example-shop · inventory · Product A

                       Warehouse    Store      Total
  Variant: Size S          12         3         15
  Variant: Size M           4         2          6
  Variant: Size L           0         1          1   ← out at Warehouse
```

Cohort:

```
example-shop · inventory · cohort: vendor:Vendor A (12 products)

  Product A     22   ✓
  Product B     14   ⚠ out at one location
  Product C      0   ✗ no stock
  …

Want one in detail? "show inventory for Product A".
```

## Edge cases

- **Untracked inventory.** Surface separately as "tracking off."
- **Many variants × many locations.** Cap displayed grid at 10 variants × 5 locations; offer to drill deeper.
- **Pagination.** Cap at 200 products in a cohort snapshot — beyond that, the merchant should narrow.

## Examples

- [`examples/basic.md`](examples/basic.md)
