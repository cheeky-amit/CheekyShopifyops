---
id: inventory.receive-shipment
name: receive-shipment
title: Receive a shipment into stock
description: Add received quantities to one or more variants at a location, with reason 'received', logged with PO reference if provided.
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
  - "receive shipment"
  - "I just received [N] of [product]"
  - "log received stock"
  - "PO arrived"
  - "shipment intake"
inputs:
  - name: items
    type: array
    description: |
      List of { target, variant, location, quantity_received }.
      Same shape as cycle-count, but quantity_received is added (not set).
  - name: po_reference
    type: string
    description: Optional purchase order reference for the run log.
outputs:
  summary: |
    Run log per item: previous → new (added: N), with PO reference. Plus rollback inverse.
safety:
  blocks: []
  requires_confirm: true
  rollback: |
    Re-call set-inventory for each item with quantity = previous, reason 'correction'.
    Inverse calls are included in the run log.
status: stable
version: 1.0.0
---

## Workflow

Composes with `primitives/safe-write`.

1. **Resolve each item** (target → product/variant, location → GID).
2. **Read current** for each (item × location).
3. **Propose:** for each, `proposed = current + quantity_received`, reason `received`.
4. **Diff render** as a multi-row table:
   ```
   example-shop · receive shipment · PO #12345

     Product A · Size S · Warehouse:    12 → 36   (+24)
     Product A · Size M · Warehouse:     4 → 28   (+24)
     Product B · Default · Warehouse:    0 → 100  (+100)
   ```
5. **Confirm.**
6. **Execute per-row with CAS** (`compareQuantity = current`). Per-row CAS catches mid-flight drift on any single line item.
7. **Log.** Run log includes PO reference if provided.

## Safety

- Each row uses CAS independently. A failure on row 2 does not block rows 1 and 3 — it's logged as "skipped, recount."
- Reason is always `received` for these adjustments. Don't mix with `restock` (that's for return-to-stock from cancellations).
- The skill caps at 50 line items per run. Larger shipments → split.

## Edge cases

- **Duplicate items in the list.** Merge before proposing.
- **Untracked variant.** Skip with a clear note ("Tracking off — turn it on first if you want this counted").
- **Negative quantity.** Reject ("To remove stock, use `inventory.cycle-count` with the new lower count.").

## Examples

- [`examples/basic.md`](examples/basic.md)
