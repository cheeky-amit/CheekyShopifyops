# Example — bulk archive a vendor's out-of-stock products

```
> Merchant: "Archive everything from Vendor A that's active and out of stock."

example-shop · bulk status sweep

  Cohort:        vendor:Vendor A AND status:active AND inventory_total:0
  Match count:   28
  Already at ARCHIVED:  0
  To change:     28
  Target status: ARCHIVED

   1.  Product A
   2.  Product B
   3.  Product C
   4.  Product D
   5.  Product E
   6.  Product F
   7.  Product G
   8.  Product H
   9.  Product I
  10.  Product J
  11.  Product K
  12.  Product L
  13.  Product M
  14.  Product N
  15.  Product O
  16.  Product P
  17.  Product Q
  18.  Product R
  19.  Product S
  20.  Product T
  21.  Product U
  22.  Product V
  23.  Product W
  24.  Product X
  25.  Product Y
  26.  Product Z
  27.  Product AA
  28.  Product BB

  Bulk write: confirmation requires explicit phrasing.
  Type "yes — archive these 28" to apply.
  Type "no" to abort.

> Merchant: "yes — archive these 28"

Re-checking 28 candidates for drift… ok.

→ bulk-update-product-status(productIds: [...28], status: ARCHIVED)

Run: catalog.bulk-status-sweep @ 2026-01-15T14:32:11Z
Store: example-shop · USD · America/Denver · Shopify plan
Cohort: vendor:Vendor A AND status:active AND inventory_total:0
Changes applied: 28 / 28

   Product A     ACTIVE → ARCHIVED   ✓
   Product B     ACTIVE → ARCHIVED   ✓
   …  (28 total)

Inverse (rollback):
   bulk-update-product-status(productIds: [...28], status: ACTIVE)

Save this run log if you want to undo. To restore: ask "undo the last bulk sweep".
```
