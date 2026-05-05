# Find an order

Plain-language order search. Ask for orders by date, value, customer email, or order number.

## What you can say

> "Find orders from this week."
> "Orders over $200."
> "ORDER-1001"
> "Orders for customer@example.com."

## What you'll see back

If a few orders match, you'll get a compact list — date, customer (first name + last initial), total, paid/fulfillment status. If exactly one order matches (e.g. you asked by order number), you'll get the full order detail instead.

## What it won't do

- Won't refund or cancel orders. Refunds are blocked at Shopify; do those in your admin.
- Won't fulfill or ship orders — there's no direct fulfillment action in this skill.
- Won't change anything — this is read-only search.

## Privacy note

Customer names in the list view are abbreviated to first name + last initial. Open a specific order to see full customer details.

## Want to see under the hood

It translates your plain-language query into an order search and returns either a list (with names abbreviated) or, on a single match, the full order.

## Related

- [`orders.daily-pulse`](../daily-pulse/) — quick read on today's sales.
- [`orders.fulfillment-watchlist`](../fulfillment-watchlist/) — orders stuck unfulfilled.
- [`customers.high-value-list`](../../customers/high-value-list/) — find a customer by lifetime spend, then look at their orders.
