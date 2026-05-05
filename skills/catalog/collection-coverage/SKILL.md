---
id: catalog.collection-coverage
name: collection-coverage
title: Collection coverage audit
description: Find products not in any collection, and collections with zero products — so storefront navigation isn't broken.
audience: merchant
roles: [merchandiser, content, founder]
category: catalog
risk: read
mcp_tools:
  - get-shop-info
  - search_products
  - search_collections
  - get-collection
graphql:
  query: false
  mutation: false
triggers:
  - "products not in any collection"
  - "orphan products"
  - "empty collections"
  - "collection coverage"
  - "audit collections"
inputs:
  - name: scope
    type: string
    default: "both"
    description: "products | collections | both"
outputs:
  summary: |
    For products: count of orphans (in 0 collections), with a sample.
    For collections: empty-collection list.
status: stable
version: 1.0.0
---

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity and `store.stage` to scale pagination caps (default 500 products / 200 collections; up to 2000 / 500 on `1m+`).
1. **Orphan products.** Read all `ACTIVE` products. For each, count its collections. Active products in 0 collections are orphans (unless the storefront uses search-only navigation).
2. **Empty collections.** `search_collections` paginated. For each collection, check `productsCount` (via `get-collection` or via search filter `product_id`).
3. Render two grouped lists.

## Output format

```
example-shop · collection coverage audit

→ Orphan products (active, in 0 collections)        18
  • Product A
  • Product B
  • …
  Want to fix? Ask "add Product A to a collection".

→ Empty collections (0 products)                     4
  • "Featured Q1"     (smart, rule didn't match anything)
  • "Coming Soon"     (manual, was likely cleared after a launch)
  • "Sale - Holiday"  (smart, rule too narrow)
  • "Test Collection" (manual, looks like leftover testing)

  Want to clean up? Empty collections still appear in the storefront menu
  if linked. You can either delete (in Shopify admin) or repopulate.
```

## Edge cases

- **Smart-collection rule mismatches.** An empty smart collection is often a rule problem, not a product problem. Surface the rule for context.
- **Search-only catalogs.** Some stores rely on search rather than collections. Surface a footnote that orphan products may be intentional.
- **Pagination caps.** Cap at 500 products and 200 collections per run.

## Examples

- [`examples/basic.md`](examples/basic.md)
