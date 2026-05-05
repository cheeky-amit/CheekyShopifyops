# Example — collection has more than 50 products

```
> Merchant: "Set everything in the Spring 2025 collection to DRAFT."

example-shop · bulk status sweep

  Cohort:        collection: "Spring 2025"
  Match count:   87
  Already at DRAFT: 4
  To change:     83
  Target status: DRAFT

  ⚠ This collection has 83 products that need to change.
    The bulk-update tool caps at 50 per call.

  Two options:
    1) Update the first 50 now, then re-run for the remaining 33.
       (Order: alphabetical by title. Re-run will pick up the rest.)
    2) Narrow the cohort. e.g. "Spring 2025 with vendor:Vendor A" — and
       run separate sweeps per slice.

  What do you want to do?

> Merchant: "Just do it in two passes."

example-shop · bulk status sweep · pass 1 of 2

  First 50 products in "Spring 2025"  (alphabetical by title)

   1.  Product A
   …
  50.  Product AX

  Type "yes — set these 50 to DRAFT" to apply.

(... after confirmation, applies; logs; tells the merchant to run pass 2 ...)
```

The skill never silently truncates. Surfacing the overflow lets the merchant decide whether to slice, two-pass, or abort.
