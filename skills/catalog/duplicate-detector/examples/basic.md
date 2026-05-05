# Example — duplicate detection

```
> Merchant: "Find duplicate products."

example-shop · duplicate detection · 248 products scanned

→ Identical SKU                                          3 clusters

  SKU "SKU-A":
    • Product A     (active,    vendor: Vendor A)
    • Product A2    (draft,     vendor: Vendor A)        ← likely import duplicate

  SKU "SKU-B":
    • Product B     (active,    vendor: Vendor A)
    • Product B-old (archived,  vendor: Vendor A)        ← intentional? archived dup

  SKU "SKU-G":
    • Product G     (active,    vendor: Vendor B)
    • Product G EU  (active,    vendor: Vendor B, tag: market-eu) ← likely intentional (region split)


→ Near-identical title                                   2 clusters

  Cluster:
    • Product C - Black     (active)
    • Product C — Black     (active)                     ← em-dash vs hyphen (likely typo dup)

  Cluster:
    • Product H             (active)
    • Product H             (active)                     ← exact same title, different IDs


→ Auto-suffixed handle                                   1 cluster

  Handle pattern: product-d, product-d-1
    • Product D     handle: product-d
    • Product D     handle: product-d-1                  ← import collision

Quick reads:
  • 4 clusters look like real duplicates.
  • 1 cluster (SKU-G) looks intentional (region split — same SKU across markets).
  • Em-dash/hyphen typos are easy fixes via update-product.

Want to clean up? Either:
  • Archive the duplicate manually: "archive Product A2"
  • Bulk-archive by ID list: `catalog.bulk-status-sweep`
```
