# Audit and fix product statuses

Find products that are in the wrong state and propose fixes:

- **Live but out of stock** with no recent sales → archive.
- **Draft** that look ready to sell (priced, in stock, in a published collection) → activate.
- **Archived** but still listed in active collections → move to draft so the storefront stays clean.

## How merchants ask for this

> "Audit my products."
> "Find products that are live but out of stock."
> "Which products are in the wrong status?"
> "Find drafts that should be live."

## How it works (short version)

1. The skill scans your products in three buckets (active-no-stock, draft-ready, archived-still-linked).
2. It checks recent sales for each candidate (last 90 days by default).
3. It shows you a grouped preview with current → proposed status and a one-line reason for each.
4. **Nothing is changed until you confirm.** And by default, the first run is a dry-run — you'd opt in for the real change.
5. After execution, you get a before/after log so you can undo if needed.

## Inputs you can adjust

- **`cohort`** (default: everything) — limit the audit, e.g. `vendor:Acme`, `tag:summer-sale`, `product_type:Apparel`.
- **`archive_no_sales_days`** (default: 90) — how long without sales before an out-of-stock active product gets flagged for archiving.
- **`dry_run`** (default: true) — when true, only previews. Set to false to actually apply changes.

## What this skill won't do

- It will not delete products. Archive is reversible; deletion isn't.
- It will not change product details (price, title, description). Only status.
- It will not touch products with inventory tracking turned off — those need manual review.
- It will not run on more than 500 candidate products at once. Narrow the cohort if your store is bigger.

## Limits and known gotchas

- `bulk-update-product-status` does not support compare-and-swap. The skill mitigates by re-checking each candidate's status immediately before writing and skipping anything that drifted.
- Sales data lookups can occasionally time out. If a product's sales lookup fails, the skill excludes it from the archive bucket rather than assuming zero sales.
- Inventory totals aggregate across all locations. A product showing `0` might just be at a closed warehouse — the skill surfaces a note when this might be the case.

## See also

- [`catalog.missing-content`](../missing-content/) — find products missing description, image, alt text.
- [`catalog.duplicate-detector`](../duplicate-detector/) — find likely duplicate products.
- [`catalog.bulk-status-sweep`](../bulk-status-sweep/) — when you already know which products to archive.
- [`inventory.snapshot`](../../inventory/snapshot/) — check stock per location if the audit results look surprising.
