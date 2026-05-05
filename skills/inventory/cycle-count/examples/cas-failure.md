# Example — CAS failure (stock changed mid-flight)

The merchant counted 18 at 14:30. They ran the skill at 14:32. Between 14:30 and 14:32, an order placed online decremented Warehouse stock by 1.

```
> Merchant: "I counted 18 of Product A at Warehouse."

example-shop · cycle count adjustment

  Product A (SKU-A) at "Warehouse"
    24 → 18    (delta: -6)
    reason:    cycle count — counted 18, system showed 24

  Type "yes" to apply.

> Merchant: "yes"

→ set-inventory(quantity 18, compareQuantity 24, reason 'cycle_count_available')

⚠ Stock changed since you counted (system now shows 23, not 24).
  An order probably went out between your count and this confirmation.

  No change applied. Two options:
    1) Recount and try again (safest).
    2) Apply 18 anyway, accepting that one in-flight sale is now reflected.
       (Treat as: floor said 18, sale removed 1 → write 17.)

  What do you want to do?
```

The skill never silently retries with the new comparison value — that defeats the protection.
