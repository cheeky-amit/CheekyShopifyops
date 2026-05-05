# Weekly merchant brief

Your Monday-morning digest. Headline numbers, top movers, what's running low, what's stuck, and a few "things worth checking this week" — all in one read so the week doesn't start with five separate questions.

## What you can say

> "Weekly brief."
> "What happened last week?"
> "Monday brief."

## What you'll see back

Five sections, in order:

1. **Headline** — sales, orders, average order value, sessions, conversion rate, returning-customer share, all versus the prior 7 days.
2. **Top movers** — your top 5 products by sales for the week.
3. **Under-performers** — your bottom 5, often a good prompt to clean up product status.
4. **Low stock** — fast-moving products at or below the threshold.
5. **Stuck orders** — paid but not shipped past 72 hours.

A short "anomalies / watch" block follows, calling out things that look unusual (e.g. sessions up but average order value down), and ends with two or three concrete suggestions for what to do this week.

## What it won't do

- Won't change anything — read-only.
- Won't email the brief or post it anywhere. Ask for it when you want it.
- Won't replace the standalone skills it draws from — each section is also available on its own if you want to dig in.

## Settings you can change

- **Window** (default: last 7 days). Any window — last 30, year-to-date, a custom range.
- **Stale threshold** for stuck orders (default: 72 hours).
- **Low-stock threshold** (default: 5 units).

## Want to see under the hood

It rolls up five smaller skills you already have — `analytics.daily-pulse`, `analytics.product-performance`, `inventory.low-stock-alert`, `orders.fulfillment-watchlist`, and `analytics.cohort-retention` — runs them for the same window, and renders one digest with a short "what to do this week" tail. Each is also available on its own if you want to dig into a single section.

## Related

- [`analytics.daily-pulse`](../../analytics/daily-pulse/) — sales numbers for any window.
- [`analytics.product-performance`](../../analytics/product-performance/) — best and worst sellers.
- [`inventory.low-stock-alert`](../../inventory/low-stock-alert/) — what's running low first.
- [`orders.fulfillment-watchlist`](../../orders/fulfillment-watchlist/) — paid orders that haven't shipped.
- [`analytics.cohort-retention`](../../analytics/cohort-retention/) — new vs returning trend.
