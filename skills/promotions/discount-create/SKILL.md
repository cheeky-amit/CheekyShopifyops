---
id: promotions.discount-create
name: discount-create
title: Create a percentage discount code
description: Create a percentage-off discount code with safe scoping (collection, customer segments, min purchase or quantity, dates).
audience: merchant
roles: [cmo, founder, cx]
category: promotions
risk: write
mcp_tools:
  - get-shop-info
  - search_collections
  - create-discount
graphql:
  query: false
  mutation: false
triggers:
  - "create a discount"
  - "make a discount code"
  - "set up a coupon"
  - "create a sale code"
  - "X% off discount"
inputs:
  - name: title
    type: string
  - name: code
    type: string
  - name: percentage
    type: number
    description: "1–100"
  - name: scope_collection
    type: string
    description: Collection name or GID. Optional — omit for all products.
  - name: customer_segments
    type: array
    description: Segment names to scope to. Omit for all customers.
  - name: minimum_purchase
    type: number
    description: Minimum order subtotal. Mutually exclusive with minimum_quantity.
  - name: minimum_quantity
    type: integer
    description: Minimum item count. Mutually exclusive with minimum_purchase.
  - name: starts_at
    type: string
    description: ISO 8601 start. Default = now (active immediately).
  - name: ends_at
    type: string
    description: ISO 8601 end. Default = no end date.
outputs:
  summary: |
    Confirmation of the created discount with scope, validity window, and the code itself.
safety:
  blocks: []
  requires_confirm: true
  rollback: |
    A discount can't be "uncreated." To deactivate immediately:
    graphql_mutation discountCodeBasicUpdate setting endsAt = now.
    The skill includes the GraphQL operation in the run log.
status: stable
version: 1.0.0
---

## Workflow

Composes with `_system/safe-write`.

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity and `operator.write_defaults` for confirm strictness — discount-create always confirms regardless because it's irreversible.
1. **Resolve scope.** If `scope_collection` is a name, search → GID. If multiple matches, ask.
2. **Validate.**
   - `percentage` 1–100.
   - `minimum_purchase` and `minimum_quantity` mutually exclusive.
   - If `starts_at` is in the past beyond a small grace window, ask the merchant — likely a typo.
   - If no `starts_at` → confirm "active immediately or scheduled?" — never assume.
3. **Diff render.**
   ```
   example-shop · create discount

     Title:        Spring Sale
     Code:         SPRING20
     Percentage:   20% off
     Scope:        Collection "Spring 2026"  (32 products)
     Customers:    All
     Minimum:      Order subtotal ≥ $50
     Validity:     2026-03-01 → 2026-03-15

     Type "yes" to create.
   ```
4. **Confirm.**
5. **Execute.** `create-discount`.
6. **Log.** Include the GraphQL rollback operation (`discountCodeBasicUpdate` to set `endsAt = now`) in the log so the merchant can deactivate fast.

## Safety

- Discounts cannot be uncreated. The skill makes this explicit in the confirm prompt.
- Default `starts_at` is *not* assumed — the skill asks. Accidentally launching a 50% off code right now is the kind of mistake that hurts.
- If the percentage is high (≥ 50%), the skill double-checks: "50% is steep. Confirm?"

## Edge cases

- **Code already exists.** `create-discount` returns an error; surface it cleanly.
- **Scope to a collection that doesn't exist.** Resolve fails; suggest collections that do.
- **Customer segment by name.** Pass-through to `create-discount`. If the segment doesn't exist, the API surfaces the error.

## Examples

- [`examples/basic.md`](examples/basic.md)
