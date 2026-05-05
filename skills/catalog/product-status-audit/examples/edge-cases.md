# Example — edge cases

All names, IDs, and dates are placeholders.

## Inventory tracking turned off

Some products (digital downloads, made-to-order) intentionally have stock tracking off. They always read as "0 in stock" even when you treat them as available. The audit never flags these.

```
> You: "Audit my products."

  248 products scanned. Proposed: 9 changes.

  Archive  9 items (details above)

  Excluded from the archive list:
    • Product P    inventory tracking is OFF — not flagged.
    • Product Q    inventory tracking is OFF — not flagged.

  If you want these audited too, turn tracking on first, or say
  "audit including untracked products" (treats tracking-off as in-stock).
```

## Too many products in one run

A single run looks at up to 500 candidates so the change list stays readable.

```
> You: "Audit my products."

  Partial result. I found more than 500 candidates, which is more than I'll
  process in one go.

  Scanned: 500 of about 860 candidates.

  Two ways forward:
    1) Narrow the group, e.g. "audit Vendor A" or "audit tag:summer-sale".
    2) Run per-vendor: I can list your vendors and we'll do them one at a time.

  Which would you like?
```

## A sales lookup didn't come back

```
  Re-checking sales for archive candidates…
    Could not get sales data for Product R — skipping it from this run.

  Final archive list: 9 products (1 skipped because the sales lookup failed).
```

We'd rather skip a product than wrongly archive it.

## Someone changed a product's status during the preview

Between when you saw the preview and when you confirmed, another teammate (or a separate workflow) changed two of the products. The skill notices and skips just those two.

```
  Re-checking 14 candidates…
    Product C is already archived now — skipping.
    Product K is already active now — skipping.

  Applying remaining 12:
     9 products → archived  ✓
     2 products → active    ✓
     1 product  → draft     ✓
```

## Undo after a partial run

If a batch failed partway through, undo only reverses what actually applied — never anything that didn't run.

```
  Run at 2026-01-15 14:32.
  Changes applied: 9 of 14 attempted.

  Failed:
    Batch of 3 → active — failed (auth error). 0 of 3 applied.
    Batch of 1 → draft  — not attempted (aborted after the failure).

  Successful changes: 9 products archived.

  To undo what did apply, say: "undo the last status audit."
  That will set those 9 products back to active.
```

— under the hood —
  Each row carries its previous status into the run log. Undo reads that log and
  applies the inverse, in batches of 50, with the same preview-and-confirm flow.
