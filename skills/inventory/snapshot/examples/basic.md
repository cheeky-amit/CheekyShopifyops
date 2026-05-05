# Example — inventory snapshot

Single product:

```
> You: "Stock for Product A."

  example-shop · Product A

                       Warehouse    Store      Total
    Size S                 12         3         15
    Size M                  4         2          6
    Size L                  0         1          1     out at Warehouse

  Total: 22 units across 3 variants and 2 locations.
```

A group of products:

```
> You: "Inventory for Vendor A."

  example-shop · Vendor A · 12 products

    Product A      22   ok
    Product B      14   out at one location (Store)
    Product C       6   ok
    Product D       0   no stock anywhere
    Product E       8   ok
    Product F      12   out at one location (Warehouse)
    Product G      18   ok
    Product H       0   tracking off — review manually
    Product I      24   ok
    Product J       4   low (under 5)
    Product K      32   ok
    Product L      14   ok

  Summary: 1 with no stock, 3 partially out, 1 with tracking off.
  Want details on one? "Show inventory for Product D."
```

— under the hood —
  Reads current stock per variant per location. No writes.
