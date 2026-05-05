# Create a percentage discount code

Set up a percent-off code with the right scope, the right window, and a built-in safety check before anything goes live.

## What you can say

> "Create a 20% off code SPRING20 for the Spring 2026 collection, valid Mar 1 to Mar 15."
> "Make a 15% discount for new customers."
> "Set up WELCOME10."

## What you'll see back

The skill shows you the full plan first: the code, the percentage, what it applies to (whole store, one collection, one customer group), any minimum cart size, and the start and end dates. Nothing is created until you confirm. After it's live, you get the run log with the exact "end this code right now" command in case the code goes wrong (more on that below).

## When it will ask before doing anything

Before creating, you'll see every detail laid out — code, percent, scope, customer group, minimum, start, end. You confirm by typing "yes." Two extra checks:

- **Steep discounts.** Anything 50% off or more triggers a second confirmation — that's near or below the floor of profitability for most products, and a lot of the time it's a typo (you meant 5%, not 50%).
- **You can't truly delete a discount once it's created.** You CAN end it immediately by setting its end date to right now. The skill always includes that exact command, copy-pasteable, in the run log — so if a code ships wrong, you can kill it in one move.

## What it won't do

- Won't create fixed-amount discounts ($10 off). Future skill.
- Won't create buy-X-get-Y discounts. Future skill.
- Won't create free-shipping discounts. Future skill.
- Won't create automatic (no-code) discounts. Future skill.
- Won't truly delete a created discount — Shopify doesn't allow it. The "end now" command is the next-best thing.

## Settings you can change

- **Code** — what customers type at checkout (e.g. SPRING20).
- **Percentage** (1–100) — the discount amount.
- **Scope** — whole store, one collection, or specific customer groups.
- **Minimum** — either a minimum cart total OR a minimum item count, not both.
- **Start and end** — when the code is live, in plain dates.

## Want to see under the hood

It validates everything you typed against the live store, shows you the plan, asks for confirmation (twice if the discount is steep), and then creates the code. The run log carries the "end now" command so undoing a mistake is one paste away.

## Related

- [`promotions.discount-audit`](../discount-audit/) — list every discount currently running, scheduled, or recently expired.
- [`promotions.collection-sale`](../collection-sale/) — same thing but scoped to a collection, with revenue context up front.
- [`analytics.daily-pulse`](../../analytics/daily-pulse/) — see how the discount affected sales after it went live.
- [`analytics.product-performance`](../../analytics/product-performance/) — what the discounted products did during the window.
