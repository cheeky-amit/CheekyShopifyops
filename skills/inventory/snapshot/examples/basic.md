# Example — inventory snapshot

Single product:

```
> Merchant: "Stock for Product A."

example-shop · inventory · Product A

                       Warehouse    Store      Total
  Variant: Size S          12         3         15
  Variant: Size M           4         2          6
  Variant: Size L           0         1          1   ← out at Warehouse

Total stock: 22 units across 3 variants and 2 locations.
```

Cohort:

```
> Merchant: "Inventory for vendor:Vendor A."

example-shop · inventory · cohort: vendor:Vendor A (12 products)

  Product A      22   ✓
  Product B      14   ⚠  out at one location (Store)
  Product C       6   ✓
  Product D       0   ✗  no stock anywhere
  Product E       8   ✓
  Product F      12   ⚠  out at one location (Warehouse)
  Product G      18   ✓
  Product H       0   ✗  tracking off — review manually
  Product I      24   ✓
  Product J       4   ⚠  low (under 5)
  Product K      32   ✓
  Product L      14   ✓

Summary: 1 with no stock, 3 partially out, 1 untracked.
Want details on one? "show inventory for Product D".
```
