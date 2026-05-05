# Weekly merchant brief

The Monday-morning digest. Headline numbers, top movers, low stock, stuck orders, and anomalies — in one read.

> "Weekly brief."
> "What happened last week?"
> "Monday brief."

## What it returns

Five sections:
1. **Headline** — sales, orders, AOV, sessions, conversion, returning rate (vs prior 7d).
2. **Top movers** — top 5 products by sales.
3. **Under-performers** — bottom 5.
4. **Low stock** — fast-moving products at or below threshold.
5. **Stuck orders** — paid but unfulfilled past 72h.
6. **Anomalies / watch** — short list of "things worth checking this week."

## How it's built

This is a composite skill. It doesn't introduce new MCP calls; it orchestrates `analytics.daily-pulse`, `analytics.product-performance`, `inventory.low-stock-alert`, `orders.fulfillment-watchlist`, and `analytics.cohort-retention`. Each of those skills is also available standalone.
