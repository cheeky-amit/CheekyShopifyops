---
id: inventory.cycle-count
name: cycle-count
title: Apply a cycle-count adjustment
description: Reconcile counted stock against the system, using compare-and-swap so a parallel sale can't get clobbered.
audience: merchant
roles: [operations, founder]
category: inventory
risk: write
mcp_tools:
  - get-shop-info
  - search_products
  - get-product
  - get-inventory-levels
  - set-inventory
graphql:
  query: false
  mutation: false
triggers:
  - "cycle count"
  - "I counted [N] of [product] at [location]"
  - "adjust inventory for cycle count"
  - "reconcile stock"
  - "I just counted and got"
inputs:
  - name: target
    type: string
    description: Product (name, SKU, or GID).
  - name: variant
    type: string
    description: Variant identifier (e.g. "Size M") if the product has multiple.
  - name: location
    type: string
    description: Location name. Resolved to GID via the product's inventory levels.
  - name: counted
    type: integer
    description: The count from the floor.
  - name: reason
    type: string
    default: "cycle_count_available"
    description: Reason for set-inventory. Default cycle_count_available; use 'shrinkage' if delta < 0 and shrinkage is the explanation; 'damaged' for damaged stock.
outputs:
  summary: |
    Run log: previous quantity → new quantity, delta, reason, timestamp. Plus rollback note.
safety:
  blocks: []
  requires_confirm: true
  rollback: |
    Re-call set-inventory with the previous quantity, compareQuantity = the new (counted) quantity,
    reason 'correction'. The skill includes the inverse call in the run log.
status: stable
version: 1.0.0
---

## Purpose

The merchant counted stock on the floor. The system shows a different number. This skill reconciles the difference safely: read current, show the delta, confirm, write with `compareQuantity`, log the rollback.

## When to use

- Routine cycle counts.
- After a stock-take.
- When the merchant says "I counted N at location X."

## When NOT to use

- The merchant received a shipment. Use `inventory.receive-shipment` (different reason code, different intent).
- The merchant wants to adjust quantity for an unspecified reason. Use this skill but pick reason carefully — `correction` is the catch-all.
- The merchant wants to bulk-reconcile dozens of items. Compose this skill in a loop, but surface a single roll-up confirmation, not 30 separate ones.

## Workflow

Composes with `primitives/safe-write`.

1. **Resolve target.** If `target` is a name/SKU, search-and-pick. If multiple matches, surface and ask. Resolve the variant + location to inventoryItemId × locationId.
2. **Read current.** `get-inventory-levels` → capture `available` for that pair as `previous`.
3. **Propose.** Build change set: `{ inventoryItemId, locationId, current: previous, proposed: counted, delta: counted - previous, reason }`.
4. **Diff render.**
   ```
   example-shop · cycle count adjustment

   Product A (SKU-A) at "Warehouse"
     24 → 18    (delta: -6)
     reason:    cycle count — counted 18, system showed 24

   Type "yes" to apply.
   ```
5. **Confirm.**
6. **Execute with CAS.** `set-inventory` with `compareQuantity: previous`. If the call fails because stock changed mid-flight, surface that and abort — ask the merchant to recount.
7. **Log.**
   ```
   Run: inventory.cycle-count @ <iso>
   Store: example-shop · USD · America/Denver · Shopify plan
   Change applied: 1
     gid://shopify/InventoryItem/000 @ Location/000   24 → 18   (cycle_count_available)

   Rollback: set-inventory(quantity 24, compareQuantity 18, reason 'correction').
   ```

## Safety

- `compareQuantity` is **required** by this skill. It's the only thing protecting against a parallel sale clobbering the count.
- If CAS fails (stock changed), abort and ask for a recount. Do NOT auto-retry with the new value — that's the bug the CAS is preventing.
- Reason defaults to `cycle_count_available`. If the delta is large and negative, the skill suggests `shrinkage` as a more honest reason — merchant chooses.
- Never adjusts more than one (item × location) per call. For bulk cycle counts, the caller (or a future composite) loops this skill.

## Edge cases

- **Untracked variant.** Inventory tracking off → can't `set-inventory`. Surface: "Tracking is off for this variant. Turn on tracking first."
- **Variant ambiguity.** "I counted 18 of Product A" — but Product A has 3 variants. Ask which.
- **Location ambiguity.** "At Warehouse" but two locations contain "Warehouse" in the name. Ask.
- **Negative result.** `counted: -1` — invalid; reject before proposing.

## Examples

- [`examples/basic.md`](examples/basic.md)
- [`examples/cas-failure.md`](examples/cas-failure.md) — what happens when stock changes mid-flight.
