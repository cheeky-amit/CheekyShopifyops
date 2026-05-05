# Top and bottom product performers

Rank your products by sales, orders, sell-through, or net sales — over any window, top down or bottom up — so you know what's actually moving and what's quietly sitting on the shelf.

## What you can say

> "Top 10 products."
> "What's not selling?"
> "Best sellers last 30 days."
> "Sell-through this quarter."

## What you'll see back

A ranked list of products for the window you asked about. Each row shows the product, its gross sales for the period, its order count, and its average order value. Bottom-of-list views also show current stock and the date of the last sale, so dead inventory is easy to spot. If you ranked by sell-through, the skill notes any products without inventory tracking — their sell-through numbers will be misleading and shouldn't be trusted.

## What it won't do

- Won't compute profit or margin. These are top-line numbers — costs aren't here.
- Won't break results down by variant. Product-level only.
- Won't include test orders or cancelled orders.

## Settings you can change

- **Range** (default: last 30 days). Any window — the last 7 days, last 90 days, month-to-date, year-to-date, or a custom date span.
- **Rank by** (default: gross sales). Options: gross sales, orders, sell-through rate, or net sales.
- **Direction** (default: top). `top` for best, `bottom` for worst.
- **How many** (default: 10). Ask for any number.

## Sell-through caveat

Sell-through depends on the store knowing how much stock you started with. Products without inventory tracking turned on don't have a real sell-through number — anything reported is misleading. The skill flags this when it matters so the rank doesn't lie to you.

## Want to see under the hood

It pulls product-level sales for the window you asked, ranks them by the chosen measure, and adds the inventory and last-sale fields when you're looking at the bottom of the list.

## Related

- [`analytics.daily-pulse`](../daily-pulse/) — overall sales trend across the same window.
- [`analytics.referrer-mix`](../referrer-mix/) — what channels drove the sales of these products.
- [`catalog.product-status-audit`](../../catalog/product-status-audit/) — flag dead products for archive (often what shows up at the bottom).
- [`inventory.snapshot`](../../inventory/snapshot/) — stock per location for the products that are moving.
