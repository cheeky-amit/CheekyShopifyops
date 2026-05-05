# Example — fulfillment watchlist

```
> You: "Show stuck orders."

  example-shop · stuck orders (stale threshold: 72h)

  Stale (paid, unfulfilled, > 72h)             4
    • ORDER-1001    92h    Customer A B.    $128.00    3 items
    • ORDER-1002    84h    Customer C D.    $54.00     1 item
    • ORDER-1003    78h    Customer E F.    $212.00    2 items
    • ORDER-1004    74h    Customer G H.    $36.00     1 item

  Today (paid, unfulfilled, < 72h)             8
    • ORDER-1005     3h    Customer I J.    $84.00     2 items
    • ORDER-1006     7h    Customer K L.    $128.00    1 item
    • … 6 more

  Partial                                       1
    • ORDER-1007    30h    Customer M N.    $172.00    2 of 3 fulfilled

  Want details on one? Ask: "show ORDER-1001."
  To fulfill an order, do it in your Shopify admin (no direct fulfillment
  action exposed here).
```

Empty case:

```
> You: "What hasn't shipped?"

  example-shop · stuck orders

  Nothing stuck. 🎉
  (0 paid-unfulfilled orders past 72h. Today's unfulfilled count: 3.)
```

— under the hood —
  Reads paid+unfulfilled orders, sorts by age, splits into stale / today /
  partial buckets, and abbreviates customer names for the list view.
