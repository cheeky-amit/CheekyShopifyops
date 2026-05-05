# Example — basic audit, preview then apply

Names, IDs, prices, and dates are placeholders. Your real run will show your actual store data.

```
> You: "Audit my products."

  Auditing example-shop. Looking at everything (no filter set).
  Preview only — no changes will be applied.

  248 products scanned. Proposed: 14 changes.

  Archive (live, out of stock, no sales in 90 days)        10
    • Product A    last sale: 2025-09-12    stock: 0
    • Product B    last sale: 2025-08-30    stock: 0
    • Product C    last sale: never         stock: 0
    • Product D    last sale: 2025-07-04    stock: 0
    • Product E    last sale: 2025-09-22    stock: 0
    • Product F    last sale: 2025-08-15    stock: 0
    • Product G    last sale: 2025-09-01    stock: 0
    • Product H    last sale: 2025-07-30    stock: 0
    • Product I    last sale: never         stock: 0
    • Product J    last sale: 2025-09-10    stock: 0

  Activate (looks ready to sell)                            3
    • Product K    priced $XX.XX, stock: 24, in published "Featured"
    • Product L    priced $XX.XX, stock: 8,  in published "New Arrivals"
    • Product M    priced $XX.XX, stock: 12, in published "Featured"

  Move to draft (archived but still in a published collection)  1
    • Product N    in published collection "Best Sellers"

  To apply for real, say: "apply the audit for real."
  To narrow: try, e.g., "audit only Vendor A."

> You: "Apply for real."

  Re-checking 14 candidates… ok, none drifted.

  Applying:
    10 products → archived  ✓
     3 products → active    ✓
     1 product  → draft     ✓

  Done. 14 changes applied at 2026-01-15 14:32.

  If you want to undo, say: "undo the last status audit."

> You: "Undo the last status audit."

  Restoring 14 products to their previous status…
  Done. 10 back to active, 3 back to draft, 1 back to archived.

— under the hood —
  Bucket scans → recent-sales lookup → preview → re-check for drift → apply
  status changes in batches of up to 50 → log every before/after row so undo
  is a single ask away.
```
