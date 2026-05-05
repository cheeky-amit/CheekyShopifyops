# Where shoppers drop off

A four-step funnel — sessions, cart adds, reached checkout, completed checkout — with the drop-off rate at each step and a one-line read on which step is hurting the most.

## What you can say

> "Conversion funnel."
> "Where are people dropping off?"
> "Checkout funnel last 30 days."

## What you'll see back

A small table that walks shoppers from session through to purchase, showing how many made it to each step and what share dropped off in between. Plus the overall conversion rate (sessions to completed checkouts) and a plain-English read on which step is the worst offender. The most useful version is the split-by-device view — most stores find that mobile drops off twice as hard as desktop, and that's usually the single most actionable insight in the report.

## What it won't do

- Won't strip out bot traffic perfectly. Some headless browsers and crawlers count as sessions in Shopify's data, so an unusually low session-to-cart rate may be partly bots, not buyers. Take the absolute numbers with a grain of salt; the trends are still useful.
- Won't show you which products people abandoned. That needs a different kind of report.
- Won't include test orders or cancelled orders.

## Settings you can change

- **Range** (default: last 30 days). Any window — the last 7 days, last 90 days, month-to-date, year-to-date, or a custom date span.
- **Split by** (optional). `device`, `country`, or `source` — surfaces where the funnel breaks worst.

## Want to see under the hood

It pulls session, cart-add, checkout-reached, and checkout-completed counts for your window, computes the drop-off at each step, and adds a brief read on which step is the bottleneck.

## Related

- [`analytics.daily-pulse`](../daily-pulse/) — overall sales trend for the same window.
- [`analytics.referrer-mix`](../referrer-mix/) — split the funnel by where shoppers came from.
- [`analytics.product-performance`](../product-performance/) — what's actually being bought when shoppers do convert.
- [`storefront.shop-snapshot`](../../storefront/shop-snapshot/) — top-of-funnel snapshot of your storefront overall.
