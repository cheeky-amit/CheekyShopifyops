# Collection coverage audit

Find products not in any collection and collections that have no products.

> "Orphan products."
> "Empty collections."
> "Collection coverage."

## What it returns

- **Orphan products** — active products in zero collections. Typically a navigation oversight; the merchant fixes by adding to one or more collections.
- **Empty collections** — collections with zero products. Either a smart-collection rule mismatch, leftover test data, or a launch that wasn't populated.

## Edge case

Some stores intentionally rely on search-only navigation. The skill flags this as a possible explanation when orphans are common.
