# Example — missing content audit

```
> Merchant: "Audit my product content."

example-shop · missing content audit · 248 products scanned

  Missing description:    14
  Missing image:           3
  Missing alt text:       42  (across 18 products)
  Missing vendor:         24
  Missing product type:   71
  Missing tags:           19

Worst offenders (missing most fields):
  • Product A   missing: description, image, vendor, type
  • Product B   missing: description, alt text, type
  • Product C   missing: image, alt text, type
  • Product D   missing: vendor, type, tags
  • Product E   missing: description, type
  …

Quick wins:
  • 3 products have no image — these will look broken on storefront. Prioritize.
  • 71 products have no type — limits how customers can filter your catalog.
  • 14 products have no description — hurts SEO and conversion.

Want to fix one? Ask "fix content on Product A" (uses update-product).
Want bulk auto-suggest? Not in v1 — content writes need careful review.
```
