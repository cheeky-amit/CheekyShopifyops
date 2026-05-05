# Example — cycle count

```
> Merchant: "I counted 18 of Product A at the Warehouse."

example-shop · cycle count adjustment

  Product A (SKU-A) at "Warehouse"
    24 → 18    (delta: -6)
    reason:    cycle count — counted 18, system showed 24

  Type "yes" to apply.

> Merchant: "yes"

→ set-inventory(quantity 18, compareQuantity 24, reason 'cycle_count_available')

Run: inventory.cycle-count @ 2026-01-15T14:32:11Z
Store: example-shop · USD · America/Denver · Shopify plan
Change applied: 1
  gid://shopify/InventoryItem/000 @ Location/000   24 → 18   (cycle_count_available)

Rollback: ask "set Product A at Warehouse back to 24" — the inverse call is:
  set-inventory(quantity 24, compareQuantity 18, reason 'correction').
```

Variant ambiguity:

```
> Merchant: "I counted 18 of Product A."

example-shop · which variant?

  Product A has multiple variants:
    • Size S    (currently 12)
    • Size M    (currently 4)
    • Size L    (currently 0)

  Which one did you count? You can also specify the location if more than one carries it.
```

Suggested reason flip:

```
> Merchant: "I counted 12 of Product B at Warehouse" (system shows 24)

  Product B at "Warehouse"
    24 → 12    (delta: -12)
    reason:    cycle count — counted 12, system showed 24

  That's a -12 delta. If the cause is theft, damage, or loss rather than a counting
  mistake, "shrinkage" is a more honest reason. Want to switch?
    • "yes, mark as shrinkage"
    • "no, keep cycle_count_available"
```
