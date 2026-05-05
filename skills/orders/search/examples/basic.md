# Example — order search

## By date range

```
> Merchant: "Show me orders from this week."

example-shop · orders matching "this week" (since 2026-01-13)

  ORDER-1010   2026-01-15   Customer A B.   $128.00   paid · unfulfilled
  ORDER-1009   2026-01-15   Customer C D.   $54.00    paid · fulfilled
  ORDER-1008   2026-01-14   Customer E F.   $212.00   paid · fulfilled
  ORDER-1007   2026-01-14   Customer G H.   $36.00    paid · unfulfilled
  ORDER-1006   2026-01-13   Customer I J.   $84.00    paid · fulfilled

5 orders matched.
```

## By value

```
> Merchant: "Orders over $200."

example-shop · orders matching "over $200"

  ORDER-1008   2026-01-14   Customer E F.   $212.00   paid · fulfilled
  ORDER-1003   2026-01-08   Customer K L.   $284.00   paid · fulfilled
  ORDER-0992   2025-12-30   Customer M N.   $456.00   paid · refunded
```

## By email

```
> Merchant: "Find orders for customer@example.com."

example-shop · orders matching email "customer@example.com"

  ORDER-1006   2026-01-13   Customer A B.   $84.00    paid · fulfilled
  ORDER-0984   2025-11-22   Customer A B.   $128.00   paid · fulfilled
```

## Single hit by name

```
> Merchant: "ORDER-1001"

# Skill calls get-order directly; the host's order widget renders.
```
