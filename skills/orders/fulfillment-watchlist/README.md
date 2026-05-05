# Stuck unfulfilled orders

Find paid orders that haven't shipped yet — oldest first — so the ones aging in your queue can't hide.

## What you can say

> "Show stuck orders."
> "What hasn't shipped?"
> "Anything in the fulfillment queue that needs attention?"

## What you'll see back

Three groups:

- **Stale** — paid, unfulfilled, older than 72 hours. These are the ones to fix today.
- **Today** — paid, unfulfilled, less than 72 hours old. Normal flow, just shown for context.
- **Partial** — orders where some items shipped but not all.

Each row shows the order number, how old it is, the customer (first name + last initial only), the total, and the line count. If nothing is stuck, you'll see "Nothing stuck. 🎉"

## What it won't do

- Won't fulfill or ship orders. There's no direct fulfillment action in this skill — that lives in your Shopify admin (or a separate, deliberate workflow).
- Won't refund or cancel orders — refunds are blocked at Shopify; do those in your admin.
- Won't change anything — this is a read-only watchlist.

## Settings you can change

- **Stale threshold** (default: 72 hours). Anything paid-and-unfulfilled past this age shows up in the stale group.
- **Include partial** (default: on). Toggle off if you don't want partial-fulfillment orders in the list.

## Privacy note

Customer names are abbreviated to first name + last initial in this list view (so "Customer A B." stands for "Customer A B[last name]"). To see a specific order's full details, ask for it by number — for example, "show ORDER-1001."

## Want to see under the hood

It reads orders in those three buckets, sorts each by age, and abbreviates customer names for the summary view.

## Related

- [`orders.daily-pulse`](../daily-pulse/) — quick read on today's sales.
- [`orders.search`](../search/) — find a specific order.
