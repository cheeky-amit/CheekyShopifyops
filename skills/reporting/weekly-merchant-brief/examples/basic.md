# Example — weekly merchant brief

```
> Merchant: "Weekly brief."

example-shop · weekly brief · 2026-01-08 → 2026-01-15 · USD

HEADLINE
  Orders:        84       (+12% vs prior 7d)
  Total sales:   $9,820   (+8%)
  AOV:           $117     (-3%)
  Sessions:      4,210    (+22%)
  Conversion:    2.0%     (-0.4 pp)
  Returning:     34%      (+4 pp)

TOP MOVERS  (top 5 by sales)
  1.  Product A    $1,820   24 orders
  2.  Product B    $1,210   18 orders
  3.  Product C    $940     12 orders
  4.  Product D    $720     8 orders
  5.  Product E    $580     6 orders

UNDER-PERFORMERS  (bottom 5)
  •  Product P    $0     0 orders    ← flag for status audit
  •  Product Q    $0     0 orders    ← out of stock since Sep 2025
  •  Product R    $40    1 order
  •  Product S    $80    2 orders
  •  Product T    $120   3 orders

LOW STOCK  (≤5, ranked by recent sales)
  •  Product A    2    18 sold last 30d    ⚠ reorder now
  •  Product B    4    12 sold
  •  Product C    1    8 sold

STUCK ORDERS  (paid + unfulfilled > 72h)
  ORDER-1001   92h    Customer A B.   $128.00
  ORDER-1002   84h    Customer C D.   $54.00
  ORDER-1003   78h    Customer E F.   $212.00
  ORDER-1004   74h    Customer G H.   $36.00

ANOMALIES / WATCH
  • AOV down 3% while sessions up 22% → more browsers, smaller carts.
    Worth checking the funnel: ask "conversion funnel last 7 days".
  • Returning rate +4 pp — repeat-purchase engine improving.
  • Product Q is ACTIVE but has had no stock since Sep 2025.
    → run "audit my products" (catalog.product-status-audit).

THIS WEEK
  • Reorder fast movers (Products A, B, C).
  • Fulfill or follow up on the 4 stuck orders.
  • Run product-status-audit when you have 5 minutes.
```
