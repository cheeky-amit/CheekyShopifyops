---
id: catalog.missing-content
name: missing-content
title: Find products missing content
description: Find products missing description, image, alt text, vendor, type, or tags — so the catalog reads professionally and SEO doesn't suffer.
audience: merchant
roles: [merchandiser, content, founder]
category: catalog
risk: read
mcp_tools:
  - get-shop-info
  - search_products
  - get-product
graphql:
  query: false
  mutation: false
triggers:
  - "products missing description"
  - "products without images"
  - "audit my product content"
  - "what products are incomplete"
  - "products missing alt text"
inputs:
  - name: cohort
    type: string
    default: "all"
  - name: checks
    type: array
    default: ["description", "image", "alt-text", "vendor", "product-type", "tags"]
    description: Subset of checks to run.
outputs:
  summary: |
    Per-check counts, plus a ranked list of products missing the most fields (worst offenders first).
status: stable
version: 1.0.0
---

## Workflow

1. Cohort via `_system/cohort-builder`.
2. Read products with `_system/data-extraction` (cap 500). For each, evaluate the requested checks. (The list response covers most fields; for `alt-text`, `get-product` is needed because it's per-image.)
3. Group by check + rank by missing-fields-count.

## Output format

```
example-shop · missing content audit · 248 products scanned

  Missing description:    14
  Missing image:           3
  Missing alt text:       42  (across 18 products)
  Missing vendor:         24
  Missing product type:   71
  Missing tags:           19

Worst offenders (missing most fields):
  • Product A   missing: description, image, vendor, type
  • Product B   missing: description, alt text, type
  • Product C   missing: image, alt text, type
  …

Want to fix one? Ask: "fix content on Product A" (uses update-product).
```

## Edge cases

- **Alt-text bulk read.** Requires per-product calls; cap at 200 if alt-text check is included.
- **Tags-empty vs tags-light.** "Missing tags" is `tags == []`. A product with one weak tag isn't "missing" — that's `tag-cleanup`'s job.
- **Vendor "default."** Stores often have a default vendor name (the shop name). Treat that as missing-equivalent if it matches the shop name exactly.

## Examples

- [`examples/basic.md`](examples/basic.md)
