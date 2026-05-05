---
id: catalog.duplicate-detector
name: duplicate-detector
title: Detect duplicate products
description: Find likely duplicate products by similar title, identical SKU, identical barcode, or near-identical handle.
audience: merchant
roles: [merchandiser, operations, founder]
category: catalog
risk: read
mcp_tools:
  - get-shop-info
  - search_products
graphql:
  query: false
  mutation: false
triggers:
  - "find duplicate products"
  - "duplicate detection"
  - "duplicate SKUs"
  - "are there duplicate listings"
  - "products with the same name"
inputs:
  - name: cohort
    type: string
    default: "all"
  - name: signals
    type: array
    default: ["sku", "barcode", "title-fuzzy", "handle"]
    description: Which signals to use.
outputs:
  summary: |
    Grouped duplicate clusters with signal explanation per cluster.
status: stable
version: 1.0.0
---

## Workflow

1. Read products via `primitives/data-extraction` (cap 500).
2. For each requested signal, build clusters:
   - **SKU**: group by SKU; clusters with size > 1 are duplicates.
   - **Barcode**: same.
   - **Title-fuzzy**: normalize titles (lowercase, strip punctuation, collapse whitespace) → group; flag near-matches with Levenshtein distance ≤ 2 across normalized titles.
   - **Handle**: handles that differ only by trailing `-1`, `-2` (Shopify auto-suffixes on collision).
3. Render clusters with the signal that grouped them.

## Output format

```
example-shop · duplicate detection · 248 products scanned

→ Identical SKU  (3 clusters)
  SKU "SKU-A":
    • Product A      (status: active)
    • Product A2     (status: draft)
  SKU "SKU-B":
    • Product B      (status: active, vendor: Vendor A)
    • Product B-old  (status: archived)
  …

→ Near-identical title  (2 clusters)
  Cluster:
    • Product A
    • Product A    (different IDs)
  Cluster:
    • Product C - Black
    • Product C — Black    (em-dash vs hyphen)

→ Auto-suffixed handle  (1 cluster)
  Handle pattern "product-d", "product-d-1":
    • Product D
    • Product D    (Shopify auto-renamed handle on collision — likely a duplicate import)

Want to merge or archive duplicates? Use `catalog.bulk-status-sweep` to archive,
or fix manually with `update-product`.
```

## Edge cases

- **Variants with the same SKU intentionally.** Some merchants reuse SKUs across regions or markets. Flag for review, don't assume duplicate.
- **Title-fuzzy false positives.** Two real products with similar names ("Black T-shirt", "Black Tee"). Surface but don't auto-recommend archiving.
- **Empty SKU/barcode.** Don't cluster on empty values — would group every product without a SKU.

## Examples

- [`examples/basic.md`](examples/basic.md)
