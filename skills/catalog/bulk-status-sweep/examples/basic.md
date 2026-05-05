# Example — bulk archive a vendor's out-of-stock products

```
> You: "Archive everything from Vendor A that's active and out of stock."

  example-shop · bulk status sweep

  Group:           Vendor A, currently active, 0 in stock
  Match count:     28
  Already archived: 0
  Will change:     28
  Target status:   archived

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

  This is a bulk change. To apply, type exactly:
    yes — archive these 28
  Or type "no" to abort.

> You: "yes — archive these 28"

  Re-checking 28 products… ok.

  Archiving 28 products… done.

  Run at 2026-01-15 14:32. 28 of 28 applied.

  If you want to undo, say: "undo the last bulk sweep."
  That will set all 28 back to active.

— under the hood —
  Search the group → list every match → wait for the explicit count-and-direction
  confirmation → re-check each product right before writing → one batched status
  change → log the inverse so undo is one ask.
```
