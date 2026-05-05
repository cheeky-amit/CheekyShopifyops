---
id: orders.search
name: search
title: Find orders
description: Find orders by status, date range, customer name or email, or value — using merchant-friendly phrasing.
audience: merchant
roles: [cx, operations, founder]
category: orders
risk: read
mcp_tools:
  - get-shop-info
  - list-orders
  - get-order
graphql:
  query: false
  mutation: false
triggers:
  - "find an order"
  - "find orders from X"
  - "orders over $XX"
  - "show me orders from this week"
  - "find orders for [customer]"
inputs:
  - name: spec
    type: string
    description: |
      Free-text search. Examples:
        "from this week"
        "over $200"
        "from customer@example.com"
        "with refund issues"
        "ORDER-1001"
  - name: limit
    type: integer
    default: 25
outputs:
  summary: |
    Compact list of matching orders. If only one match → full detail via get-order.
status: stable
version: 1.0.0
---

## Purpose

A friendly search front-end for orders. Merchants don't know Shopify search syntax; this skill translates plain language into the right `list-orders` query.

## When to use

- Merchant has a vague description ("the order I refunded last Tuesday for the Canadian customer").
- Merchant has a specific order name or number.
- Merchant wants a date range that isn't today / yesterday / week.

## When NOT to use

- Merchant wants today's pulse → `orders.daily-pulse`.
- Merchant wants stuck orders → `orders.fulfillment-watchlist`.

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity and `shop.currency` for parsing money phrases.
1. Parse `spec` into a Shopify order search query. Pattern matches:
   - Order name/number (`ORDER-1001`, `1001`, `#1001`) → call `get-order` directly with the number.
   - Email substring → `email:*<value>*`.
   - Date range words ("this week", "last 30 days") → `created_at:>='<iso>'`.
   - Money ("over $200", "under $50") → `total_price:>=200` / `total_price:<=50`. Honor shop currency.
   - Status words ("paid", "refunded", "open", "cancelled") → `financial_status:` / `status:`.
2. If query is unambiguous and likely returns one result, use `get-order` for full detail.
3. Else `list-orders` with the query, capped at `limit`.
4. Render compact list. Always abbreviate customer last name to initial (privacy).

## Output format

```
example-shop · orders matching "<spec>"

  ORDER-1001   2026-01-14   Customer A B.    $128.00    paid · unfulfilled
  ORDER-1002   2026-01-13   Customer C D.    $54.00     paid · fulfilled
  …

To see one in detail: "show ORDER-1001".
```

For single match, fall through to full `get-order` widget output.

## Edge cases

- **Ambiguous spec.** "From last week" could mean created or fulfilled last week. Ask the merchant which.
- **No results.** "No orders match `<spec>`. Try widening the date range or check the spelling."
- **Customer with multiple matches by email pattern.** Surface all matches; let merchant disambiguate.
- **Currency mismatch.** If the merchant says "over €100" but the shop is in USD, ask them to confirm — or convert if the shop has multi-currency markets enabled (this v1 doesn't auto-convert; just flag).

## Examples

- [`examples/basic.md`](examples/basic.md)
