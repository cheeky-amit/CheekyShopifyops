# Inventory snapshot

A clear picture of what you have on hand right now — for one product, or a whole group — broken down by variant and location.

## What you can say

> "What's my stock?"
> "Inventory snapshot for Product A."
> "Stock for Vendor A."

## What you'll see back

Two shapes, depending on what you ask:

- **Single product** — a grid of variants by location, with totals. Useful for "do I have any size M left at the warehouse?"
- **Group of products** — per-product totals with flags for "out at one location," "out everywhere," "low," or "tracking is off."

## What it won't do

- Won't change stock — this is read-only. To adjust, see `inventory.cycle-count` or `inventory.receive-shipment`.
- Won't show numbers for products with inventory tracking turned off — those products are surfaced separately as "tracking off" so they don't get mistaken for "out of stock."

## Want to see under the hood

It looks up current stock for each variant at each location and rolls it up. No writes.

## Related

- [`inventory.low-stock-alert`](../low-stock-alert/) — what's running low, ranked by recent sales.
- [`inventory.cycle-count`](../cycle-count/) — adjust stock after a count.
- [`inventory.receive-shipment`](../receive-shipment/) — add stock when a delivery arrives.
