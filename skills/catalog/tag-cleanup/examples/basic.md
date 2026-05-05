# Example — tag cleanup

```
> Merchant: "Audit my tags."

example-shop · tag cleanup proposal · 248 products scanned · 187 unique tags

→ Case-duplicate clusters             3 clusters · 42 products

  "Sale"      "sale"   "SALE"        → "sale"          28 products
  "New"       "new"                  → "new"           10 products
  "Featured"  "FEATURED"             → "featured"       4 products

→ Near-duplicate clusters             2 clusters · 18 products

  "Tshirt"    "T-shirt"   "tshirts"  → "t-shirt"      12 products
  "Hoodie"    "hoodies"              → "hoodie"        6 products

→ Single-use tags (review only)       7

  "snmmer"                          1 product   ← likely typo of "summer"
  "blcuk"                           1 product   ← likely typo of "black"
  "limited-edition-2024-spring"     1 product   ← maybe intentional
  "vendor-acme-discontinued"        1 product   ← maybe intentional
  "tmp-do-not-use"                  1 product   ← cleanup target
  "test"                            1 product
  "x"                               1 product

→ Smart-collection conflicts          1 warning

  "Featured" tag is used as a rule on smart collection "Featured Products".
  Renaming "Featured" → "featured" will break the rule.
  The skill will skip this cluster — fix the collection rule first, then re-run.

  Effective changes if you confirm: 2 case-duplicate clusters + 2 near-duplicate clusters
                                    = 4 clusters, 56 products affected.

  Type "yes" to apply.

> Merchant: "yes"

Re-checking 56 affected products for drift… 1 drifted (skipped: Product Z).

Applied:
  ✓ 28 products: "Sale"/"sale"/"SALE" → "sale"
  ✓ 10 products: "New"/"new"          → "new"
  ✓ 12 products: "Tshirt"/"T-shirt"/"tshirts" → "t-shirt"
  ✓  6 products: "Hoodie"/"hoodies"   → "hoodie"

Skipped:
  ⚠ 1 product (Product Z) — tags drifted since preview. Review manually.
  ⚠ 1 cluster ("Featured") — smart-collection conflict. Fix rule first.

Run log saved. To undo: ask "undo the last tag cleanup" — the skill will set
each affected product back to its previous tag list.
```
