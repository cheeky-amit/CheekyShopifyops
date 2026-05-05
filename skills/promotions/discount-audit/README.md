# What discounts are running

A clean list of your discount codes — what's live now, what's coming up, and (if you ask) what recently expired.

## What you can say

> "What discounts do I have running?"
> "Audit my discount codes."
> "Any active sales?"

## What you'll see back

Three groups, in order:

- **Active now** — currently usable at checkout.
- **Upcoming** — scheduled but not yet started.
- **Expired** — past their end date. Hidden by default; ask "include expired" to see them.

Each row shows the code, what kind of discount it is (percent off, free shipping, buy-X-get-Y), what it applies to, the dates, and how many times it's been used so far. Steep discounts get a small flag next to them so they don't slip past you.

## What it won't do

- Won't change or end any discount. This is read-only.
- Won't always show app-driven discounts. If you use Shopify apps for discounts — like a subscription app or a flash-sale app — those may not show up here. The skill flags this when it detects discount apps installed.

## Settings you can change

- **Include expired** (default: off). Turn on to see the last 30 days of ended codes.

## Want to see under the hood

It pulls every discount on your store, sorts each into active/upcoming/expired, and notes when an outside app might be running discounts the audit can't see.

## Related

- [`promotions.discount-create`](../discount-create/) — create a new percent-off code.
- [`promotions.collection-sale`](../collection-sale/) — plan a discount on one collection with revenue context.
- [`analytics.daily-pulse`](../../analytics/daily-pulse/) — sales trend so you can tie discount activity to results.
- [`analytics.product-performance`](../../analytics/product-performance/) — top products during a discount window.
