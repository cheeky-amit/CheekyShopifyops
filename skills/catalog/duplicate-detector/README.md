# Find duplicate products

Spot products that look like duplicates of each other — usually leftovers from a botched import or a region split.

## What you can say

> "Find duplicate products."
> "Duplicate SKUs."
> "Are there duplicate listings?"

## What you'll see back

Clusters of likely duplicates, grouped by why we think they match: same SKU, same barcode, near-identical title (after ignoring case and punctuation), or auto-suffixed handle (where Shopify added a "-1" or "-2" because the URL was already taken — a strong signal of an import that didn't dedupe). Each cluster gets a short read on whether it looks like a real duplicate or an intentional split (same SKU across markets, for example).

## What it won't do

- Won't merge or archive duplicates on its own — too easy to drop the wrong one.
- Won't change products in any way. It surfaces clusters; you decide what to do.

## Want to see under the hood

It scans your products, normalizes titles, groups by the four signals (SKU, barcode, fuzzy title, handle pattern), and labels likely-real duplicates separately from likely-intentional splits.

## Related

- [`catalog.bulk-status-sweep`](../bulk-status-sweep/) — archive the duplicates you decide to drop.
- [`catalog.product-status-audit`](../product-status-audit/) — broader status audit.
