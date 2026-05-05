# Example — order search

By date range:

```
> You: "Show me orders from this week."

  example-shop · orders since 2026-01-13

    ORDER-1010    2026-01-15    Customer A B.    $128.00    paid · unfulfilled
    ORDER-1009    2026-01-15    Customer C D.    $54.00     paid · fulfilled
    ORDER-1008    2026-01-14    Customer E F.    $212.00    paid · fulfilled
    ORDER-1007    2026-01-14    Customer G H.    $36.00     paid · unfulfilled
    ORDER-1006    2026-01-13    Customer I J.    $84.00     paid · fulfilled

  5 orders.
```

By value:

```
> You: "Orders over $200."

  example-shop · orders over $200

    ORDER-1008    2026-01-14    Customer E F.    $212.00    paid · fulfilled
    ORDER-1003    2026-01-08    Customer K L.    $284.00    paid · fulfilled
    ORDER-0992    2025-12-30    Customer M N.    $456.00    paid · refunded
```

By email:

```
> You: "Find orders for customer@example.com."

  example-shop · orders for customer@example.com

    ORDER-1006    2026-01-13    Customer A B.    $84.00     paid · fulfilled
    ORDER-0984    2025-11-22    Customer A B.    $128.00    paid · fulfilled
```

Single match by order number — you get the full order detail:

```
> You: "ORDER-1001"

  ORDER-1001 · 2026-01-12 · Customer A B. (customer@example.com)
    Total:        $128.00
    Status:       paid · fulfilled
    Items:        3
      • Product A · Size M · $48.00
      • Product B · Default · $40.00
      • Product C · Size L · $40.00
    Ship to:      [address on file]
```

— under the hood —
  Reads your orders matching the query → returns a list (names abbreviated) or
  the full single-order detail when exactly one matches.
