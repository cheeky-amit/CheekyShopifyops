# Example — win-back list

```
> Merchant: "Build a win-back list."

example-shop · win-back list · lapsed >6 months, ≥2 past orders, subscribed

Cohort summary:
  Count:           86
  Avg lifetime:    $284.00
  Avg past orders: 3.4
  Avg lapse time:  9.2 months

Top 30 by lifetime spend:

   1.  Customer A B.    $1,820.00   5 orders   last: 2025-04-12   ← high-value, 9mo lapsed
   2.  Customer C D.    $1,210.00   4 orders   last: 2025-05-22
   3.  Customer E F.    $940.00     6 orders   last: 2025-03-08
   4.  Customer G H.    $720.00     3 orders   last: 2025-06-01
   5.  Customer I J.    $580.00     4 orders   last: 2025-04-30
   …  (25 more)

Next steps:
  • Send a re-engagement email — needs an email MCP (not in this bundle).
    See meta.wiring for adding one.
  • Create a re-engagement discount: "create discount WINBACK15 15% off
    customers in segment 'win-back'" (uses promotions.discount-create with
    customer_segments).
  • Export full list (names + emails): ask "export win-back list" —
    that's the privileged view.
```
