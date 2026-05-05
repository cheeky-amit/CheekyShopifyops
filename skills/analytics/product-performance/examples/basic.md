# Example — top products

```
> Merchant: "Top 10 products last 30 days."

example-shop · top 10 products by gross sales · 2025-12-16 to 2026-01-15 · USD

   1.  Product A    $4,820   72 orders    $67 AOV
   2.  Product B    $3,210   45 orders    $71
   3.  Product C    $2,940   38 orders    $77
   4.  Product D    $2,580   61 orders    $42
   5.  Product E    $2,210   24 orders    $92
   6.  Product F    $1,940   31 orders    $63
   7.  Product G    $1,720   28 orders    $61
   8.  Product H    $1,540   18 orders    $86
   9.  Product I    $1,380   22 orders    $63
  10.  Product J    $1,210   15 orders    $81

Want the bottom?  Ask "bottom 10 products last 30 days".
By sell-through? Ask "top 10 by sell-through last 30 days".
```

Bottom direction:

```
> Merchant: "What's not selling?"

example-shop · bottom 10 products by gross sales · last 30 days · USD

   1.  Product P    $0      0 orders    in stock: 24    last sale: never
   2.  Product Q    $0      0 orders    in stock: 12    last sale: 2025-09-04
   …

Some of these may be flagged by `catalog.product-status-audit` for archiving.
```
