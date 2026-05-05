# Example — first-order anniversary

```
> You: "Who's coming up on their one-year with us?"

example-shop · first-order anniversary · 1 year ± 14 days · subscribed only

How many:  24 customers
Window:    first orders between 2025-01-01 and 2025-01-29

  •  Customer A B.   first order 2025-01-15   $XX.XX     1 order
  •  Customer C D.   first order 2025-01-09   $XXX.XX    2 orders
  •  Customer E F.   first order 2025-01-22   $XX.XX     1 order
  •  Customer G H.   first order 2025-01-28   $XXX.XX    3 orders
  •  Customer I J.   first order 2025-01-03   $XX.XX     1 order
  •  Customer K L.   first order 2025-01-19   $XX.XX     2 orders
  …  (18 more)

What you can do next:
  • Send a thank-you email — needs an email tool, which isn't in this
    bundle. See meta.wiring for adding one alongside.
  • Create an anniversary discount: "create a 15% code for this group,
    minimum $50."
  • See one of these people in full (name + email): "show #1".
```

— under the hood —

It looks up each customer's first-ever order, keeps those whose first order falls inside the 1-year-±14-day window from today, filters to subscribed customers, and abbreviates names for the summary view.
