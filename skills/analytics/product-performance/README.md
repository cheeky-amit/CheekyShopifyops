# Top and bottom product performers

Rank products by gross sales, orders, sell-through rate, or net sales over any date range.

> "Top 10 products."
> "What's not selling?"
> "Best sellers last 30 days."
> "Sell-through this quarter."

## Inputs

- `range` — date range (default last-30d).
- `rank_by` — `gross-sales`, `orders`, `sell-through-rate`, `net-sales`.
- `direction` — `top` (default) or `bottom`.
- `limit` — default 10.

## Sell-through caveat

Sell-through is a function of inventory tracking. Untracked products produce misleading sell-through rates; the skill surfaces a footnote when this matters.
