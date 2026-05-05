# System primitives

**Internal building blocks.** Not called directly by merchants. Used by other skills via composition. If you're a merchant browsing, you can ignore this folder — these are the internal helpers other skills use.

- `shop-context/` — Loads shop profile (currency, plan, timezone). Almost every other skill calls this first.
- `graphql-helper/` — Wraps schema → docs → validate → execute for any GraphQL hop.
- `safe-write/` — Preview/diff/confirm/log/rollback scaffolding. Required by every write skill.
- `cohort-builder/` — Reusable patterns for building product/customer cohorts via search syntax.
- `data-extraction/` — Pagination-aware bulk read with the 50-per-call cap baked in.
