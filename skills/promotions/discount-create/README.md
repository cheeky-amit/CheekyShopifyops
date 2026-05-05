# Create a percentage discount code

Create a percentage-off discount with safe scoping.

> "Create a 20% off code SPRING20 for the Spring 2026 collection, valid Mar 1–15."
> "Make a discount code."

## What it supports

- Percentage off (1–100).
- Scope to a specific collection.
- Scope to specific customer segments.
- Minimum purchase amount OR minimum item count (mutually exclusive).
- Schedule via `starts_at` and `ends_at`.

## What it doesn't (yet)

- Fixed-amount discounts (e.g. "$10 off") — needs GraphQL `discountCodeBasic` with `value: DiscountAmount`. Future skill.
- BXGY (buy X get Y) — needs GraphQL `discountCodeBxgy`.
- Free shipping — needs GraphQL `discountCodeFreeShipping`.
- Automatic (non-code) discounts.

For these, fall through to `primitives.graphql-helper`.

## Important

Discounts cannot be uncreated. The fastest way to deactivate is to set `endsAt = now` via GraphQL — the skill outputs that mutation in the run log so you can run it instantly if a code goes wrong.
