# Example — basic audit, dry-run then apply

All names, IDs, prices, and dates below are placeholders. Real merchant runs will show your actual store data.

## Step 1 — merchant asks

> Merchant: "Audit my products."

## Step 2 — skill activates and shows context

```
Auditing example-shop.myshopify.com.
Cohort: everything (no filter set).
Dry-run: ON (no changes will be applied).
```

## Step 3 — skill runs the three bucket queries and previews

```
Audit on example-shop.myshopify.com · 248 products scanned

Proposed: 14 changes

  → ARCHIVE   (active, no stock, no sales in 90 days)            10 items
    • Product A     (was: ACTIVE)   last sale: 2025-09-12,  stock: 0
    • Product B     (was: ACTIVE)   last sale: 2025-08-30,  stock: 0
    • Product C     (was: ACTIVE)   last sale: never,       stock: 0
    • Product D     (was: ACTIVE)   last sale: 2025-07-04,  stock: 0
    • Product E     (was: ACTIVE)   last sale: 2025-09-22,  stock: 0
    • Product F     (was: ACTIVE)   last sale: 2025-08-15,  stock: 0
    • Product G     (was: ACTIVE)   last sale: 2025-09-01,  stock: 0
    • Product H     (was: ACTIVE)   last sale: 2025-07-30,  stock: 0
    • Product I     (was: ACTIVE)   last sale: never,       stock: 0
    • Product J     (was: ACTIVE)   last sale: 2025-09-10,  stock: 0

  → ACTIVATE  (looks ready to sell)                              3 items
    • Product K     (was: DRAFT)    priced $XX.XX,  stock: 24,  in published collection "Featured"
    • Product L     (was: DRAFT)    priced $XX.XX,  stock: 8,   in published collection "New Arrivals"
    • Product M     (was: DRAFT)    priced $XX.XX,  stock: 12,  in published collection "Featured"

  → MOVE TO DRAFT  (archived but still in published collections) 1 item
    • Product N     (was: ARCHIVED) in collection: "Best Sellers" (published)

To apply for real, ask: "apply the audit for real" or set dry_run to false.
To narrow: ask, e.g., "audit only vendor:Vendor A".
```

## Step 4 — merchant opts in to apply

> Merchant: "Apply for real."

## Step 5 — skill re-checks for drift, batches, and writes

```
Re-checking 14 candidates for drift… ok, no drift detected.

Applying:
  Batch 1/3: 10 products → ARCHIVED   ✓ applied
  Batch 2/3: 3 products  → ACTIVE     ✓ applied
  Batch 3/3: 1 product   → DRAFT      ✓ applied

Run log:
  Run: catalog.product-status-audit @ 2026-01-15T14:32:11Z
  Store: example-shop.myshopify.com
  Cohort: everything
  Changes applied: 14

    gid://shopify/Product/000   ACTIVE   → ARCHIVED
    gid://shopify/Product/001   ACTIVE   → ARCHIVED
    gid://shopify/Product/002   ACTIVE   → ARCHIVED
    gid://shopify/Product/003   ACTIVE   → ARCHIVED
    gid://shopify/Product/004   ACTIVE   → ARCHIVED
    gid://shopify/Product/005   ACTIVE   → ARCHIVED
    gid://shopify/Product/006   ACTIVE   → ARCHIVED
    gid://shopify/Product/007   ACTIVE   → ARCHIVED
    gid://shopify/Product/008   ACTIVE   → ARCHIVED
    gid://shopify/Product/009   ACTIVE   → ARCHIVED
    gid://shopify/Product/010   DRAFT    → ACTIVE
    gid://shopify/Product/011   DRAFT    → ACTIVE
    gid://shopify/Product/012   DRAFT    → ACTIVE
    gid://shopify/Product/013   ARCHIVED → DRAFT

Save this if you might want to undo. To revert, ask: "undo the last status audit".
```

## Step 6 — optional rollback

> Merchant: "Undo the last status audit."

The skill reads back the run log from the conversation context, builds the inverse mapping, and runs `bulk-update-product-status` to restore each product's previous status, batching to the 50-per-call cap.
