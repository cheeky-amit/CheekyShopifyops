# cohort-builder

**Internal building block.** Not invoked directly by merchants. Used by other skills.

Translates merchant-typed filters ("vips", "lapsed in Canada", "summer collection products that are draft") into valid Shopify search queries for products, customers, and orders. Bakes in guardrails against the field-sensitive footguns in Shopify search syntax — unknown fields silently return all records, bare-name customer searches match against notes / tags / addresses, and so on.

## Used by

Any domain skill that accepts a filter input — most catalog, customer, order, retention, and reporting skills.

## What it does

- Takes a free-text or named cohort and returns `filter` (the search query string), `label` (human-readable for confirmation prompts), and `named_cohort` (true if it matched a catalog entry).
- Maintains a catalog of named cohorts (`vips`, `lapsed`, `repeat-buyers`, `subscribers`, etc.) so multi-filter or date-computed groups are reusable.
- Refuses to construct queries with unknown fields rather than silently broadening the result set.

## Adding named cohorts

Edit the catalog in `SKILL.md` and add a row. Include rationale in the PR. Don't add aliases for cohorts the merchant can express with one filter — only add when the named cohort either composes multiple filters or computes a date.

## See also

- [SKILL.md](SKILL.md) — full agent-facing instructions and the named-cohort catalog.
- [`_system/data-extraction`](../data-extraction/) — paginated reads that consume these filters.
