# Example — edge cases

All names, IDs, prices, and dates below are placeholders.

## Untracked inventory product

A product with `inventoryItem.tracked: false` always reports `inventory_total: 0` even when the merchant considers it "in stock" (a common pattern for digital downloads or made-to-order items). The audit must not flag these.

```
Audit on example-shop.myshopify.com · 248 products scanned

Proposed: 9 changes
  → ARCHIVE   9 items   (details above)

Excluded from archive bucket:
  • Product P  — inventory tracking is OFF; status not flagged.
  • Product Q  — inventory tracking is OFF; status not flagged.

If you want these audited too, turn on inventory tracking first or
ask me to "audit including untracked products" (treats inventory-off
as in-stock).
```

## Pagination cap reached

The skill caps total candidates at 500 per run. If buckets exceed that, the merchant sees:

```
Audit on example-shop.myshopify.com — partial result

I found more than 500 candidate products, which is more than I'll process in
a single run to keep the diff readable.

I scanned: 500 / ~860 candidates.

Two ways forward:
  1) Narrow the cohort, e.g. "audit vendor:Vendor A" or "audit tag:summer-sale".
  2) Run the audit per-vendor: I can list your vendors and we'll do them one at a time.

Which would you like?
```

## Sales lookup failed for one product

```
Re-checking sales for archive candidates…
  ⚠  Could not get sales data for Product R (gid://shopify/Product/000) — skipped.

Final archive bucket: 9 products (1 excluded due to lookup failure).
```

The excluded product is reported but never assumed to have zero sales. Better to skip than to wrongly archive.

## Mid-flight drift — someone else changed a status between preview and execute

```
Re-checking 14 candidates for drift…
  ⚠  Product C (gid://shopify/Product/002) is now ARCHIVED already.   Skipping.
  ⚠  Product K (gid://shopify/Product/010) is now ACTIVE already.     Skipping.

Applying remaining 12:
  Batch 1/3: 9 products  → ARCHIVED   ✓
  Batch 2/3: 2 products  → ACTIVE     ✓
  Batch 3/3: 1 product   → DRAFT      ✓
```

## Rollback after a partial-failure run

```
Run: catalog.product-status-audit @ 2026-01-15T14:32:11Z
Store: example-shop.myshopify.com
Changes applied: 9 of 14 attempted

Failed batches:
  Batch 2/3: 3 products → ACTIVE — failed (auth error). 0 of 3 applied.
  Batch 3/3: 1 product  → DRAFT  — not attempted (aborted on previous failure).

Successful changes:
  gid://shopify/Product/000  ACTIVE → ARCHIVED
  gid://shopify/Product/001  ACTIVE → ARCHIVED
  …  (9 total)

To roll back what did apply, ask: "undo the last status audit".
The skill will set those 9 products back to ACTIVE.
```

The rollback only inverts what actually applied — never invents writes for batches that didn't run.
