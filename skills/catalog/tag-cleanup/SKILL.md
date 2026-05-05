---
id: catalog.tag-cleanup
name: tag-cleanup
title: Audit and consolidate product tags
description: Find tag taxonomy issues — typos, near-duplicates, case-only duplicates, single-use tags — and propose a consolidation per cluster.
audience: merchant
roles: [merchandiser, content, founder]
category: catalog
risk: write
mcp_tools:
  - get-shop-info
  - search_products
  - update-product
graphql:
  query: false
  mutation: false
triggers:
  - "audit my tags"
  - "tag cleanup"
  - "fix tag typos"
  - "consolidate tags"
  - "tag taxonomy"
inputs:
  - name: cohort
    type: string
    default: "all"
  - name: rules
    type: array
    default: ["case-duplicate", "near-duplicate", "single-use"]
    description: Which checks to run.
  - name: confidence_threshold
    type: number
    default: 0.85
    description: For near-duplicate detection (0..1).
outputs:
  summary: |
    Per-cluster proposed consolidation. Each cluster: list of source tags, target tag, affected
    product count. Single confirm prompt approves all clusters.
safety:
  blocks: []
  requires_confirm: true
  rollback: |
    Run log includes inverse: per product, the previous tag list. To undo:
    update-product on each affected product with the original tags. Skill can do this on request.
status: stable
version: 1.0.0
---

## Workflow

Composes with `primitives/safe-write`.

1. **Read tag distribution.** Read products in cohort via `primitives/data-extraction`. Build histogram of tags.
2. **Cluster by signal.**
   - **case-duplicate**: `Sale`, `sale`, `SALE` → cluster, target = lowercase canonical.
   - **near-duplicate**: trigram or Levenshtein similarity above threshold (`Tshirt`, `T-shirt`, `tshirts`) → cluster, target = most-used spelling.
   - **single-use**: tags applied to exactly one product → flag for review (often typos).
3. **Propose.** Per cluster: source tags, target tag, affected product count. Don't auto-include the merchant's "intentionally rare" tags — surface single-use tags as a review-only list, no auto-consolidate.
4. **Diff render.**
   ```
   example-shop · tag cleanup proposal · cohort: all

   → Case-duplicate clusters                         3 clusters · 42 products affected
     "Sale" / "sale" / "SALE"             → "sale"        (28 products)
     "New" / "new"                        → "new"         (10 products)
     "Featured" / "FEATURED"              → "featured"    (4 products)

   → Near-duplicate clusters                         2 clusters · 18 products affected
     "Tshirt" / "T-shirt" / "tshirts"     → "t-shirt"     (12 products)
     "Hoodie" / "hoodies"                 → "hoodie"      (6 products)

   → Single-use tags (review only — not auto-changed) 7
     "snmmer" (1 product)   ← likely typo
     "blcuk"  (1 product)   ← likely typo
     "limited-edition-2024-spring" (1)   ← intentional?
     ...

   Type "yes" to apply the case-duplicate and near-duplicate consolidations.
   Single-use tags are NOT auto-changed — review and fix individually if you want.
   ```
5. **Confirm + execute.** For each affected product, `update-product` with the consolidated tag list. Cap at 200 products per run; surface partial completion if cohort exceeds.
6. **Log.** Per product: previous tag list → new tag list. Inverse op is the previous tag list.

## Safety

- `update-product` does not support compare-and-swap. The skill re-reads each product's current tag list immediately before writing and compares to the cached version. If tags drifted (someone added a tag in the meantime), skip that product with a "drifted — review manually" note.
- Single-use tags are never auto-consolidated. Many are intentional (e.g., per-launch tags); the skill surfaces them so the merchant can fix typos manually.
- The skill caps at 200 products per run. For larger taxonomies, split.

## Edge cases

- **Tag used as a smart-collection rule.** Renaming a tag breaks the smart collection. The skill warns when a tag in a cluster appears as a `TAG` rule on any active smart collection — and either skips that cluster or surfaces a follow-up step ("update collection rule to use the new canonical").
- **Empty resulting tag list.** Skip — never strip all tags off a product.
- **Tag with semantic meaning.** "sale-50-off" near-matches "sale-15-off" — these are distinct on purpose. Tune the threshold or whitelist.

## Examples

- [`examples/basic.md`](examples/basic.md)
