# Plan a sale on one collection

A friendlier way to discount a single collection — names instead of IDs, recent revenue shown up front so you know what you're discounting, and a built-in safety check before anything goes live.

## What you can say

> "Run a 20% off sale on the Spring 2026 collection, Mar 1 to Mar 15."
> "Discount the Spring collection."
> "Plan a sale on the New Arrivals collection."

## What you'll see back

The skill looks up the collection by name (no ID hunting), shows you what it's sold in the last 30 days so you know the revenue at stake, suggests a sensible code if you didn't pick one, and lays out the full plan. Nothing is created until you confirm.

## When it will ask before doing anything

Before creating, you'll see the collection (with product count), recent revenue, the discount percent, the code, the start and end dates, and a rough estimate of margin given up at this collection's pace. You confirm by typing "yes." Two extra checks:

- **Steep discounts.** Anything 50% off or more triggers a second confirmation.
- **You can't truly delete a discount once it's created.** You CAN end it immediately by setting its end date to right now. The skill always includes that exact command, copy-pasteable, in the run log.

## What it won't do

- Won't discount more than one collection at once. One sale at a time.
- Won't create fixed-amount or buy-X-get-Y or free-shipping discounts. Future skills.
- Won't truly delete a created discount — Shopify doesn't allow it. The "end now" command is the next-best thing.

## Settings you can change

- **Collection** — by name. The skill resolves it.
- **Percentage** (1–100) — the discount amount.
- **Code** (optional) — what customers type. Auto-suggested if you skip it.
- **Start and end** — when the sale runs.
- **Minimum** (optional) — minimum cart size if you want one.

## Want to see under the hood

It looks up the collection by name, pulls its recent sales for context, lays out the plan, and asks for confirmation. On approval it creates the code and emits the "end now" command for emergency use.

## Related

- [`promotions.discount-create`](../discount-create/) — create a discount with full control over scope (not just collections).
- [`promotions.discount-audit`](../discount-audit/) — see everything currently running, including this one.
- [`analytics.product-performance`](../../analytics/product-performance/) — how the discounted collection's products did.
- [`analytics.daily-pulse`](../../analytics/daily-pulse/) — overall sales trend during the sale window.
