# Example — weekly merchant brief

```
> You: "Weekly brief."

example-shop · weekly brief · 2026-01-08 → 2026-01-15 · USD

HEADLINE
  Orders:        84       (+12% vs prior 7d)
  Total sales:   $X,XXX   (+8%)
  AOV:           $XXX     (-3%)
  Sessions:      4,210    (+22%)
  Conversion:    2.0%     (-0.4 pp)
  Returning:     34%      (+4 pp)

TOP MOVERS  (top 5 by sales)
  1.  Product A    $X,XXX   24 orders
  2.  Product B    $X,XXX   18 orders
  3.  Product C    $XXX     12 orders
  4.  Product D    $XXX     8 orders
  5.  Product E    $XXX     6 orders

UNDER-PERFORMERS  (bottom 5)
  •  Product P    $0       0 orders    ← worth a status check
  •  Product Q    $0       0 orders    ← out of stock for months
  •  Product R    $XX      1 order
  •  Product S    $XX      2 orders
  •  Product T    $XXX     3 orders

LOW STOCK  (≤ 5, ranked by recent sales)
  •  Product A    2 left   18 sold last 30d   ⚠ reorder now
  •  Product B    4 left   12 sold
  •  Product C    1 left   8 sold

STUCK ORDERS  (paid + unfulfilled > 72h)
  ORDER-1001   92h    Customer A B.   $XXX.XX
  ORDER-1002   84h    Customer C D.   $XX.XX
  ORDER-1003   78h    Customer E F.   $XXX.XX
  ORDER-1004   74h    Customer G H.   $XX.XX

ANOMALIES / WATCH
  • Average order value down 3% while sessions up 22% — more browsers,
    smaller carts. Worth a look at the funnel: ask "conversion funnel
    last 7 days."
  • Returning rate +4 pp — repeat-purchase engine looking healthier.
  • Product Q is showing as active but has had no stock for months.
    Try "audit my products."

THIS WEEK
  • Reorder fast movers (Products A, B, C).
  • Fulfill or follow up on the 4 stuck orders.
  • Run a product status audit when you have 5 minutes.
```

— under the hood —

It runs the five underlying skills (`analytics.daily-pulse`, `analytics.product-performance`, `inventory.low-stock-alert`, `orders.fulfillment-watchlist`, `analytics.cohort-retention`) for the same window, gathers their outputs, and renders them as one digest with a "this week" tail.
