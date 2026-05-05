# Audit and fix product statuses

Find products that are in the wrong state — live but selling nothing, drafts that should be live, archived items still showing in collections — and fix them in one safe sweep.

## What you can say

> "Audit my products."
> "Which products are in the wrong status?"
> "Find drafts that should be live."

## What you'll see back

You'll get a grouped preview: products to archive (live but out of stock with no recent sales), products to activate (drafts that look ready to sell), and products to move to draft (archived but still linked from a published collection). Every row shows the current status, the proposed status, and a one-line reason. Nothing changes from this preview alone — you decide whether to apply.

## When it will ask before doing anything

The first run is preview-only by default. To actually change anything, you have to opt in by saying something like "apply for real." Before that, you'll see the full count and direction (e.g. "12 to archive, 3 to activate, 1 to move to draft"). Bulk changes need an explicit confirmation that includes the count — "yes — apply these 14" — so you can never accidentally trigger 500 status changes with a casual "ok."

## What it won't do

- Won't delete products. Archive is reversible; deletion isn't.
- Won't change prices, titles, or descriptions — only status.
- Won't touch products with inventory tracking turned off — those need manual review.
- Won't process more than 500 candidate products in one run. If your store is bigger, narrow the group first.

## Settings you can change

- **Group of products** (default: everything). Limit by vendor, tag, or product type — e.g. "audit only Vendor A."
- **No-sales window** (default: 90 days). How long without a sale before an out-of-stock active product is flagged for archiving.
- **Preview-only** (default: on). The first run never writes; you opt in to apply.

## Want to see under the hood

It searches your catalog in three buckets, looks up recent sales for each candidate, and writes status changes a few at a time — so a single failure doesn't take down the run — only after you confirm. Every run leaves a full log with before/after status and an "undo" path you can ask for in plain words.

## Related

- [`catalog.missing-content`](../missing-content/) — products missing description, image, or alt text.
- [`catalog.duplicate-detector`](../duplicate-detector/) — likely duplicate listings.
- [`catalog.bulk-status-sweep`](../bulk-status-sweep/) — when you already know which products to archive.
- [`inventory.snapshot`](../../inventory/snapshot/) — check stock per location if the audit looks surprising.
