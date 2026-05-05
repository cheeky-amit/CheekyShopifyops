# Example — receive shipment

```
> You: "Received PO #12345 at Warehouse: 24 each of Product A Size S, M, L; 100 of Product B."

  example-shop · receive shipment · PO #12345

    Product A · Size S · Warehouse:    12 →   36   (+24)
    Product A · Size M · Warehouse:     4 →   28   (+24)
    Product A · Size L · Warehouse:     0 →   24   (+24)
    Product B · Default · Warehouse:    0 →  100  (+100)

  4 lines. Reason: received.
  Type "yes" to apply.

> You: "yes"

  Applying...
    Product A · Size S · Warehouse:   12 → 36   ✓
    Product A · Size M · Warehouse:    4 → 28   ✓
    Product A · Size L · Warehouse:    0 → 24   ✓
    Product B · Default · Warehouse:   0 → 100  ✓

  Done. PO #12345 received at Warehouse — 4 of 4 lines applied.
  If you want to undo this PO, say: "undo PO #12345."
```

When one line had a sale during the run:

```
  Applying...
    Product A · Size S · Warehouse:   12 → 36   ✓
    Product A · Size M · Warehouse:   stock changed since read (was 4, now 3).
                                      Skipped — please recount and re-add 24.
    Product A · Size L · Warehouse:    0 → 24   ✓
    Product B · Default · Warehouse:   0 → 100  ✓

  3 of 4 lines applied. 1 skipped because a sale shipped during this run.
```

— under the hood —
  Each line: read current stock → preview the new total → write with a per-line
  safety check so any in-flight sale on that one line is held back instead of
  silently merged. The run log carries every previous number so undo is one ask.
