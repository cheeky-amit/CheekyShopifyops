# Example — missing content audit

```
> You: "Audit my product content."

  example-shop · missing content · 248 products scanned

  Missing description:    14
  Missing image:           3
  Missing alt text:       42  (across 18 products)
  Missing vendor:         24
  Missing product type:   71
  Missing tags:           19

  Worst offenders (most fields missing):
    • Product A    missing description, image, vendor, type
    • Product B    missing description, alt text, type
    • Product C    missing image, alt text, type
    • Product D    missing vendor, type, tags
    • Product E    missing description, type

  Quick wins:
    • 3 products have no image — these look broken on the storefront. Fix first.
    • 71 products have no type — limits how customers filter your catalog.
    • 14 products have no description — hurts SEO and conversion.

  Want to fix one? Ask, e.g., "fix content on Product A".
  Bulk auto-suggest isn't in this version — content writes need careful review.
```

— under the hood —
  Reads each product's fields (title, description, images, alt text, vendor, type, tags) and groups the gaps. No writes.
