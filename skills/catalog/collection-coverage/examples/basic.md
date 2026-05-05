# Example — collection coverage

```
> Merchant: "Audit my collections."

example-shop · collection coverage audit

→ Orphan products  (active, in 0 collections)          18
  • Product A
  • Product B
  • Product C
  • Product D
  • Product E
  • … (13 more)

  These won't appear in collection-based navigation.
  Add to a collection: "add Product A to Featured" (uses add-to-collection).

→ Empty collections  (0 products)                       4
  • "Featured Q1"      smart, rule:  tag:q1-launch       (no product has this tag)
  • "Coming Soon"      manual, last updated 2025-09-12   (likely cleared after launch)
  • "Sale - Holiday"   smart, rule:  tag:holiday-sale    (rule too narrow or expired)
  • "Test Collection"  manual, last updated 2024-11-30   (looks like leftover testing)

  Cleanup options:
    • Repopulate: add products that fit, or fix the smart rule.
    • Delete: remove the collection (do this in Shopify admin — this skill won't delete).

Note: orphan products are fine if your storefront uses search-only navigation.
```
