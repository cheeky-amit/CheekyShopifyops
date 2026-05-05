# Example — low stock alert

```
> Merchant: "What's running low?"

example-shop · low stock alert · threshold ≤5 (excluding out-of-stock)

   Product       Stock   30d sales   Note
   ─────────────────────────────────────────────────────
   Product A        2          18    ⚠ fast mover, reorder now
   Product B        4          12    reorder soon
   Product C        1           8
   Product D        3           4
   Product E        5           1    slow — fine to wait

5 products at or below threshold.
Want to bring in stock? Use inventory.receive-shipment after the PO arrives.
```

Cohort + custom threshold:

```
> Merchant: "Anything in vendor:Vendor A under 10?"

example-shop · low stock alert · threshold ≤10 · cohort: vendor:Vendor A

   Product       Stock   30d sales   Note
   ─────────────────────────────────────────────────────
   Product A        2          18    ⚠ fast mover
   Product F        7          22    ⚠ fast mover, watch
   Product B        4          12
   Product J        9           6
   Product G        8           1    slow

Want to plan a reorder list to send your Vendor A contact?
You can copy this list — but the actual PO send needs your supplier
workflow (out of MCP scope).
```
