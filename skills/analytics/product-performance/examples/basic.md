# Example — top products

```
> You: "Top 10 products last 30 days."

  example-shop · top 10 by gross sales · 2025-12-16 to 2026-01-15 · USD

     1.  Product A    $4,820   72 orders    $67 avg
     2.  Product B    $3,210   45 orders    $71
     3.  Product C    $2,940   38 orders    $77
     4.  Product D    $2,580   61 orders    $42
     5.  Product E    $2,210   24 orders    $92
     6.  Product F    $1,940   31 orders    $63
     7.  Product G    $1,720   28 orders    $61
     8.  Product H    $1,540   18 orders    $86
     9.  Product I    $1,380   22 orders    $63
    10.  Product J    $1,210   15 orders    $81

  Want the bottom?    Ask "bottom 10 products last 30 days."
  By sell-through?    Ask "top 10 by sell-through last 30 days."

— under the hood —
  Pulls product-level sales for the window, ranks by the chosen measure,
  and adds inventory + last-sale fields when you're looking at the bottom.
```

Bottom direction — dead products:

```
> You: "What's not selling?"

  example-shop · bottom 10 by gross sales · last 30 days · USD

     1.  Product P    $0     0 orders    in stock: 24    last sale: never
     2.  Product Q    $0     0 orders    in stock: 12    last sale: 2025-09-04
     3.  Product R    $20    1 order     in stock: 8     last sale: 2026-01-02
     …

  Some of these are good candidates to archive. The catalog audit can
  group them: ask "audit my products."
```

Sell-through with caveat:

```
> You: "Top 10 by sell-through last 90 days."

  example-shop · top 10 by sell-through · last 90 days

     1.  Product A    92%     started with 80, sold 74
     2.  Product C    88%     started with 50, sold 44
     …

  Heads up: 4 of your products don't have inventory tracking turned on.
  Their sell-through numbers can't be trusted and were excluded from this
  ranking.
```
