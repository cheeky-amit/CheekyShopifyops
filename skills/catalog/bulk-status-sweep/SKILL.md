---
id: catalog.bulk-status-sweep
name: bulk-status-sweep
title: Bulk-archive (or activate) by cohort
description: Move many products to a target status at once — the targeted-cohort sibling of product-status-audit. Used when the merchant already knows what to change.
audience: merchant
roles: [merchandiser, founder]
category: catalog
risk: bulk-write
mcp_tools:
  - get-shop-info
  - search_products
  - search_collections
  - get-collection
  - bulk-update-product-status
graphql:
  query: false
  mutation: false
triggers:
  - "archive products in [cohort]"
  - "set [cohort] to active"
  - "bulk archive"
  - "bulk activate"
  - "bulk draft"
inputs:
  - name: cohort
    type: string
    description: Required. Cohort spec — e.g. "vendor:Vendor A AND status:active AND inventory_total:0", or a collection name.
  - name: target_status
    type: string
    description: "ACTIVE | DRAFT | ARCHIVED"
  - name: cap
    type: integer
    default: 50
    description: Max products to change in one run. Capped at 50 by the underlying tool — and intentionally not paginated past that, to keep blast radius small.
outputs:
  summary: |
    Per-batch run log with before/after status. Inverse mapping included.
safety:
  blocks: []
  requires_confirm: true
  rollback: |
    Run log includes the inverse mapping. Re-run with the inverse to restore previous statuses.
status: stable
version: 1.0.0
---

## Workflow

Composes with `primitives/safe-write`.

1. **Resolve cohort** via `primitives/cohort-builder`. If cohort matches a collection, also offer the "all products in this collection" path (using `bulk-update-product-status` with `collectionId`, which the tool caps at 50 anyway).
2. **Read** the matching products via `primitives/data-extraction`. Cap at 50.
3. **Filter to needs-change.** Drop products already at `target_status`.
4. **Diff render** with explicit cohort echo:
   ```
   example-shop · bulk status sweep

     Cohort:        vendor:Vendor A AND status:active AND inventory_total:0
     Match count:   28
     Already at ARCHIVED: 0
     To change:     28
     Target status: ARCHIVED

     • Product A
     • Product B
     • …  (full list — bulk writes always show every record by name)

     Type "yes — archive these 28" to apply.
     Type "no" or "preview only" to abort.
   ```
5. **Bulk-write confirmation requires unambiguous phrasing.** "ok" alone is not enough. The skill requires explicit echo of the count and direction (e.g., "yes — archive these 28").
6. **Re-check** for drift right before the call.
7. **Execute** `bulk-update-product-status` with the productIds (≤ 50).
8. **Log.** Per-product before/after, plus inverse mapping.

## Safety

- **Cap at 50.** Hard cap. The `bulk-update-product-status` tool caps at 50 itself; this skill does not paginate past that to keep blast radius small. For larger sweeps the merchant runs the skill again with a narrower cohort.
- **No collection > 50 silent truncation.** When cohort is a collection with more than 50 products, the underlying tool only updates the first 50. The skill detects this and warns explicitly: "Collection has 87 products; only 50 will be updated. Re-run for the rest, or narrow the cohort."
- **Confirmation strictness.** Bulk writes require explicit confirmation including the count and direction.
- **No mixed-target sweeps.** This skill applies one target status to all products in the cohort. To make multiple changes (some archive, some activate), use `catalog.product-status-audit` instead.

## Edge cases

- **Empty cohort.** Stop after step 1 with "Nothing matches `<cohort>`."
- **Already-at-target.** Filter out before showing the diff. "5 of the 28 are already ARCHIVED — only 23 will change."
- **Drift between preview and execute.** Re-check catches it; drifted products skipped with a per-product note.
- **Tool returns partial failure.** `bulk-update-product-status` updates per-product, so partial failures are possible. Log distinguishes applied/failed.

## Examples

- [`examples/basic.md`](examples/basic.md)
- [`examples/collection-overflow.md`](examples/collection-overflow.md)
