# What's running low

Products at or below a stock threshold, ranked by recent sales velocity — so the fast movers you need to reorder are at the top.

## What you can say

> "What's running low?"
> "Low-stock alert."
> "What do I need to reorder?"

## What you'll see back

A ranked list. Each row shows the product, current stock, recent 30-day sales, and a one-line note. Fast movers running low are flagged for urgent attention. Slow movers running low are still listed — you'll just see "fine to wait" next to them.

## Why ranked by recent sales

Running low on a slow mover is fine. Running low on a fast mover is a fire. The ranking puts the fires at the top so you don't bury a real reorder behind ten products that nobody buys anyway.

## What it won't do

- Won't place a reorder. There's no supplier or 3PL connection in this skill. You'd copy the list and use whatever supplier workflow you already have.
- Won't change stock — this is read-only.

## Settings you can change

- **Threshold** (default: 5). Anything at or below this count is flagged.
- **Group of products** (default: everything). Limit by vendor, tag, or product type.
- **Include out-of-stock** (default: off). By default the list is "low but not yet zero" — turn this on if you want zero counted too.

## Want to see under the hood

It reads current stock and recent 30-day sales for every product in your group, filters to those at or below your threshold, and sorts by sales velocity (fastest movers first).

## Related

- [`inventory.snapshot`](../snapshot/) — full per-location stock picture.
- [`inventory.receive-shipment`](../receive-shipment/) — add stock when a delivery arrives.
