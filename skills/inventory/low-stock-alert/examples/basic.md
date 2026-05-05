# Example — low stock alert

```
> You: "What's running low?"

  example-shop · low stock · 5 or fewer in stock (zero excluded)

   Product       Stock   30d sales   Note
   ─────────────────────────────────────────────────────
   Product A        2          18    fast mover — reorder now
   Product B        4          12    reorder soon
   Product C        1           8
   Product D        3           4
   Product E        5           1    slow — fine to wait

  5 products at or below 5.
  When the delivery lands, use "inventory.receive-shipment" to add it.
```

A custom threshold and a group:

```
> You: "Anything from Vendor A under 10?"

  example-shop · low stock · 10 or fewer · Vendor A

   Product       Stock   30d sales   Note
   ─────────────────────────────────────────────────────
   Product A        2          18    fast mover
   Product F        7          22    fast mover — watch closely
   Product B        4          12
   Product J        9           6
   Product G        8           1    slow

  Want a reorder list for Vendor A? Copy this list — sending the actual
  purchase order needs your supplier workflow (out of scope here).
```

— under the hood —
  Read current stock and recent 30-day sales → filter to threshold → sort by velocity. No writes.
