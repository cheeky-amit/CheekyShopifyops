# data-extraction (primitive)

**Internal building block.** Pagination-aware bulk read for any list/search tool or GraphQL connection.

## Used by

Most domain skills that audit or report — `catalog.product-status-audit`, `catalog.missing-content`, `inventory.snapshot`, `inventory.low-stock-alert`, `customers.cohort-snapshot`, `orders.fulfillment-watchlist`, etc.

## What it does

- Iterates cursor pagination correctly (no off-by-one, no missed last page).
- Caps at a configurable record limit (default 500) so an audit on a 100k-product store doesn't run forever.
- Returns a result object that distinguishes "hit the cap" from "hit the end" — callers must surface "hit cap" to the merchant.
- Detects runaway loops (cursor not advancing, or way more iterations than expected) and aborts cleanly.

## Why this exists

Pagination bugs are subtle and dangerous. A skill that misses the last page silently is worse than one that crashes — the merchant trusts the partial output. Centralizing pagination once and using it everywhere prevents that.
