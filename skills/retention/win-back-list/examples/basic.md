# Example — win-back list

```
> You: "Build a win-back list."

example-shop · win-back list · lapsed >6 months, ≥2 past orders, subscribed only

The group:
  How many:           86 customers
  Avg lifetime spend: $XXX.XX
  Avg past orders:    3.4
  Avg time since:     9.2 months

Top 30 by lifetime spend:

   1.  Customer A B.   $X,XXX.XX   5 orders   last bought 9 months ago
   2.  Customer C D.   $X,XXX.XX   4 orders   last bought 8 months ago
   3.  Customer E F.   $XXX.XX     6 orders   last bought 11 months ago
   4.  Customer G H.   $XXX.XX     3 orders   last bought 7 months ago
   5.  Customer I J.   $XXX.XX     4 orders   last bought 10 months ago
   …  (25 more)

What you can do next:
  • Send a re-engagement email — needs an email tool, which isn't in this
    bundle. See meta.wiring for adding one alongside.
  • Create a discount scoped to this group: "create a 15% code for the
    win-back group" (uses promotions.discount-create).
  • See one of these people in full (name + email): "show #1".
```

— under the hood —

It pulls customers whose last order is older than 6 months and who have at least 2 past orders and are subscribed to marketing. The list is ranked by lifetime spend; names are abbreviated for the summary view.
