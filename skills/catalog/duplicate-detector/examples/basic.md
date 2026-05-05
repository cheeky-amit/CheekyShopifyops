# Example — duplicate detection

```
> You: "Find duplicate products."

  example-shop · duplicate detection · 248 products scanned

  Same SKU                                            3 clusters

    SKU "SKU-A":
      • Product A     active,    Vendor A
      • Product A2    draft,     Vendor A         likely import duplicate

    SKU "SKU-B":
      • Product B     active,    Vendor A
      • Product B-old archived,  Vendor A         intentional? archived dup

    SKU "SKU-G":
      • Product G     active,    Vendor B
      • Product G EU  active,    Vendor B  tag market-eu   likely intentional (region split)

  Near-identical title                                2 clusters

    Cluster:
      • Product C - Black     active
      • Product C — Black     active            em-dash vs hyphen — likely typo dup

    Cluster:
      • Product H             active
      • Product H             active            same title, different products

  Auto-suffixed handle                                1 cluster

    Pattern: product-d, product-d-1
      • Product D     handle: product-d
      • Product D     handle: product-d-1        import collision

  Quick read:
    • 4 clusters look like real duplicates.
    • 1 cluster (SKU-G) looks intentional (region split).
    • Em-dash vs hyphen is an easy fix.

  Want to clean up?
    • Archive one: "archive Product A2."
    • Bulk-archive a list: catalog.bulk-status-sweep.
```

— under the hood —
  Reads your catalog → normalizes titles and handles → groups on four signals (SKU, barcode, fuzzy title, handle pattern). No writes.
