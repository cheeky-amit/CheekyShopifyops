# Example — collection coverage

```
> You: "Audit my collections."

  example-shop · collection coverage

  Orphan products (active, in 0 collections)                  18
    • Product A
    • Product B
    • Product C
    • Product D
    • Product E
    • … 13 more

  These won't appear in collection-based navigation on your storefront.
  To add one: "add Product A to Featured."

  Empty collections (0 products)                               4
    • "Featured Q1"     smart, auto-rule needs tag:q1-launch — no product has that tag
    • "Coming Soon"     manual, last updated 2025-09-12 — likely cleared after launch
    • "Sale - Holiday"  smart, auto-rule needs tag:holiday-sale — rule too narrow or expired
    • "Test Collection" manual, last updated 2024-11-30 — looks like leftover testing

  Cleanup options:
    • Repopulate: add products that fit, or fix the smart rule.
    • Delete: remove the collection in your Shopify admin (this skill won't delete).

  Note: orphan products are fine if your storefront uses search-only navigation.
```

— under the hood —
  Active products → which collections they're in. Collections → which products match. Two cross-referenced lists, no writes.
