# Today's order pulse

A quick five-line read on how today is going. Ask it any time of day.

## What you can say

> "How are sales today?"
> "Give me a pulse."
> "What happened yesterday?"

## What you'll see back

Five lines: order count, total sales, average order value, paid orders, and unfulfilled count (with the age of the oldest unfulfilled inlined). Compared to yesterday, so you know if today is on pace, ahead, or behind. Empty days get an "0 today" message with yesterday's number for context.

## What it won't do

- Won't change anything — this is a read-only summary.
- Won't show multi-day reports — that's for `analytics.daily-pulse`.
- Won't search for a specific order — that's `orders.search`.
- Won't show stuck unfulfilled orders by age — that's `orders.fulfillment-watchlist`.

## Want to see under the hood

It reads today's orders (and yesterday's, for the comparison) and rolls them up into the six-line summary.

## Related

- [`orders.fulfillment-watchlist`](../fulfillment-watchlist/) — which orders are stuck.
- [`orders.search`](../search/) — find a specific order.
- [`analytics.daily-pulse`](../../analytics/daily-pulse/) — multi-day trend.
