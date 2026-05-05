# Example — wrap a single-record inventory write

Caller is `inventory.cycle-count`. The merchant counted SKU-A at the warehouse and got 18 units; the system shows 24.

## Step-by-step

```
1. read_fn:
   → get-inventory-levels(productId: gid://shopify/Product/000)
   → returns: { inventoryItemId: gid://shopify/InventoryItem/000,
                locationId:      gid://shopify/Location/000,
                available:       24 }
   before = { item: ..., location: ..., available: 24 }

2. propose_fn(before):
   → change set: [{ inventory_item: ..., location: ...,
                    current: 24, proposed: 18,
                    reason: "cycle count — counted 18, system showed 24, delta -6" }]

3. Diff render (merchant-facing):
     example-shop · USD · America/Denver · Shopify plan

     Cycle count adjustment proposed:

       Product A (SKU-A) at "Warehouse"
         24 → 18    (delta: -6)
         reason:    cycle count — counted 18, system showed 24

     Type "yes" to apply.

4. Confirm: merchant types "yes".

5. CAS recheck:
   → set-inventory(
       inventoryItemId: ...,
       locationId:      ...,
       quantity:        18,
       compareQuantity: 24,            ← from `before`
       reason:          'cycle_count_available'
     )
   If stock changed since step 1, the call fails safely and we surface:
     "Stock changed since the count (now: 22). Re-count and try again."

6. Execute: set-inventory call returns ok.

7. Log:
   Run: inventory.cycle-count @ 2026-01-15T14:32:11Z
   Store: example-shop · USD · America/Denver · Shopify plan
   Changes proposed: 1
   Changes applied:  1
   Failed:           0

     gid://shopify/InventoryItem/000 @ Location/000   24 → 18   (cycle_count_available)

   Rollback: re-run set-inventory with quantity 24, compareQuantity 18,
            reason 'correction'.

8. Merchant sees log + "Save this if you want to undo."
```
