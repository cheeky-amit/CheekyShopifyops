# Example — daily pulse

```
> You: "Give me a pulse."

  example-shop · today, 2026-01-15

    Orders:        12          (+3 vs yesterday)
    Total sales:   $1,248.00   (+$310.00 vs yesterday)
    AOV:           $104.00     (+$2.00 vs yesterday)
    Paid:          11
    Unfulfilled:   8           (oldest: 2 hours)

  Want details on the unfulfilled? Ask: "show stuck orders."
  Or for the full list today: "show today's orders."
```

Empty case:

```
> You: "How are sales today?"

  example-shop · today, 2026-01-15

  No orders yet today. (Yesterday: 9 orders, $924.00.)
```

— under the hood —
  Reads today's and yesterday's orders, totals them, and shows the diff.
