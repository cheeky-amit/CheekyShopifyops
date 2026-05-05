# Sales over a date range

A trend view of sales, orders, average order value, sessions, and conversion — for any window — with an automatic comparison to the previous period of the same length.

## What you can say

> "Sales last 30 days."
> "Weekly sales."
> "Sales for 2026-01-01 to 2026-01-15."

## What you'll see back

A short summary of the period — orders, total sales, average order value, sessions, conversion — each with the percent change versus the previous period. A time-series chart underneath, so you can see the shape of the trend, not just the totals. If your store is on the Basic plan, you'll see sales-only metrics (orders, total, average order value) — sessions and conversion need Shopify or higher. The skill tells you which kind you're getting.

## What it won't do

- Won't break sales down by product. Use the product performance skill for that.
- Won't break sales down by traffic source. Use the referrer mix skill for that.
- Won't compute profit. These are top-line numbers — costs and margin aren't here.
- Won't include test orders or cancelled orders.

## Settings you can change

- **Range** — today, yesterday, the last 7 / 30 / 90 days, month-to-date, quarter-to-date, year-to-date, or any custom window like `2026-01-01..2026-01-15`.

## Want to see under the hood

It pulls daily totals across the range, computes the matching previous-period totals, and renders both the summary and the time-series. On Basic plans it skips session and conversion and tells you so.

## Related

- [`analytics.product-performance`](../product-performance/) — which products drove the period.
- [`analytics.referrer-mix`](../referrer-mix/) — which channels drove the period.
- [`analytics.conversion-funnel`](../conversion-funnel/) — where shoppers dropped off in the period.
- [`orders.daily-pulse`](../../orders/daily-pulse/) — quick read on today specifically.
