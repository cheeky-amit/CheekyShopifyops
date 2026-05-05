# data-extraction

**Internal building block.** Not invoked directly by merchants. Used by other skills.

Pagination-aware bulk read for any list/search tool or GraphQL connection. Centralizes cursor handling, record caps, and runaway-loop detection so domain skills don't each re-invent (and subtly break) their own pagination.

## Used by

Most skills that audit or report — `catalog.product-status-audit`, `catalog.missing-content`, `inventory.snapshot`, `inventory.low-stock-alert`, `customers.cohort-snapshot`, `customers.high-value-list`, `orders.fulfillment-watchlist`, `analytics.product-performance`, retention skills, and the weekly brief.

## What it does

- Iterates cursor pagination correctly — no off-by-one, no missed last page.
- Caps at a configurable record limit (default 500) so an audit on a 100k-product store doesn't run forever.
- Returns a result object that distinguishes "hit the cap" from "hit the end." Callers are required to surface "hit cap" to the merchant.
- Detects runaway loops (cursor not advancing, or way more iterations than expected) and aborts cleanly.

## See also

- [SKILL.md](SKILL.md) — full agent-facing instructions.
- [`_system/cohort-builder`](../cohort-builder/) — produces the filter inputs that drive most paginated reads.
