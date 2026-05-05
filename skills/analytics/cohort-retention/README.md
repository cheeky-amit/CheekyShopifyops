# New vs returning customers

How much of your business comes from people who've bought before — and whether that share is growing or slipping.

## What you can say

> "New vs returning customers."
> "Customer retention."
> "Returning customer rate over time."

## What you'll see back

The returning-customer rate for the period (with the change versus the previous period of the same length), a count of new vs returning customers, a time-series chart of the trend, and a one-line read on whether things are improving. If the rate looks unusually high, the skill will ask whether you run subscription products — those tend to skew this number upward, since every renewal reads as a "returning" purchase.

## What it won't do

- Won't compute lifetime value or retention curves by signup month. Those are heavier reports a future skill will cover.
- Won't filter by product or channel. Whole-store only in v1.
- Won't include test orders or cancelled orders.

## Settings you can change

- **Range** (default: last 90 days). Any window — `last-30d`, `last-180d`, `ytd`, or a custom date span.

## Heads up — small stores

Stores with less than about three months of order history have unstable retention numbers. The sample is too small for the rate to mean much — one customer coming back can swing it by several points. The skill will tell you if you're in that zone, so you don't read too much into early movement.

## Want to see under the hood

It pulls orders for the window, splits them into "first ever" and "repeat" purchases, computes the returning rate per period, and adds a brief read on the trend.

## Related

- [`customers.cohort-snapshot`](../../customers/cohort-snapshot/) — profile of your repeat-buyer group.
- [`customers.high-value-list`](../../customers/high-value-list/) — top spenders by name.
- [`analytics.daily-pulse`](../daily-pulse/) — overall sales trend for the same window.
- [`retention.win-back-list`](../../retention/win-back-list/) — lapsed customers worth re-engaging.
