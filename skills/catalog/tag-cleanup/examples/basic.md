# Example — tag cleanup

```
> You: "Audit my tags."

  example-shop · tag cleanup · 248 products scanned · 187 unique tags

  Case duplicates                3 clusters · 42 products
    "Sale" "sale" "SALE"         → "sale"        28 products
    "New"  "new"                 → "new"         10 products
    "Featured" "FEATURED"        → "featured"     4 products

  Near duplicates                2 clusters · 18 products
    "Tshirt" "T-shirt" "tshirts" → "t-shirt"     12 products
    "Hoodie" "hoodies"           → "hoodie"       6 products

  Single-use tags (review only)  7
    "snmmer"                     1 product   likely typo of "summer"
    "blcuk"                      1 product   likely typo of "black"
    "limited-edition-2024-spring" 1 product  maybe intentional
    "vendor-acme-discontinued"   1 product   maybe intentional
    "tmp-do-not-use"             1 product   cleanup target
    "test"                       1 product
    "x"                          1 product

  Heads up: collection conflict  1 warning
    "Featured" is part of a smart collection's auto-rule on "Featured Products."
    Renaming it would break that collection — we'll skip this cluster.
    Fix the collection rule in your Shopify admin first, then re-run.

  If you confirm: 4 clusters apply (2 case + 2 near-duplicate),
                  56 products updated.

  Type "yes" to apply.

> You: "yes"

  Re-checking 56 affected products… 1 drifted (Product Z) — skipping it.

  Applied:
    28 products: "Sale"/"sale"/"SALE" → "sale"      ✓
    10 products: "New"/"new"          → "new"       ✓
    12 products: "Tshirt"/"T-shirt"/"tshirts" → "t-shirt"  ✓
     6 products: "Hoodie"/"hoodies"   → "hoodie"    ✓

  Skipped:
    Product Z — tags changed since the preview. Review it manually.
    "Featured" cluster — collection conflict. Fix the rule first.

  Run log saved. To undo, say: "undo the last tag cleanup."

— under the hood —
  Build the tag histogram → cluster by case + similarity → check smart-collection
  rules → preview → re-check each product → write the new tag list per product →
  log every product's previous tags so undo is one ask.
```
