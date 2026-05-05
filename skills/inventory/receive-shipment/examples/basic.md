# Example — receive shipment

```
> Merchant: "Received PO #12345 at Warehouse:
             24 each of Product A Size S, Size M, Size L; 100 of Product B."

example-shop · receive shipment · PO #12345

  Product A · Size S · Warehouse:    12 →   36   (+24)
  Product A · Size M · Warehouse:     4 →   28   (+24)
  Product A · Size L · Warehouse:     0 →   24   (+24)
  Product B · Default · Warehouse:    0 →  100  (+100)

  4 lines. Reason: received.
  Type "yes" to apply.

> Merchant: "yes"

Applying...
  ✓ Product A · Size S · Warehouse:    12 → 36   (CAS ok)
  ✓ Product A · Size M · Warehouse:     4 → 28   (CAS ok)
  ✓ Product A · Size L · Warehouse:     0 → 24   (CAS ok)
  ✓ Product B · Default · Warehouse:    0 → 100  (CAS ok)

Run: inventory.receive-shipment @ 2026-01-15T14:32:11Z
PO:   #12345
Store: example-shop · USD · America/Denver · Shopify plan
Lines applied: 4 / 4

Rollback (if needed):
  set-inventory each back to its previous quantity with reason 'correction'.
  The skill can run that for you — ask: "undo PO #12345".
```

Partial failure on one line:

```
  ✓ Product A · Size S · Warehouse:    12 → 36   (CAS ok)
  ⚠ Product A · Size M · Warehouse: stock changed since read (was 4, now 3).
                                     Skipped — please recount and re-add 24.
  ✓ Product A · Size L · Warehouse:     0 → 24   (CAS ok)
  ✓ Product B · Default · Warehouse:    0 → 100  (CAS ok)

3 of 4 lines applied. 1 skipped due to mid-flight stock change.
```
