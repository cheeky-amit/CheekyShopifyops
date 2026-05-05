# Apply a cycle-count adjustment

Reconcile counted stock against the system, safely.

> "I counted 18 of Product A at the Warehouse."
> "Cycle count for SKU-A: 18."

## Why this skill exists separately

Inventory adjustments without compare-and-swap are dangerous. If you set the count to 18 between someone placing an order and the system decrementing it, you'll silently overcount by one. This skill always uses `compareQuantity` — the write fails if stock changed since you read it.

## What it does

1. Reads current stock for the variant × location.
2. Shows you the delta (system → counted, e.g. 24 → 18).
3. You confirm.
4. Writes with `compareQuantity` so a parallel sale can't get clobbered.
5. If a parallel sale happened, the write fails safely and asks for a recount.

## Rollback

The run log includes the inverse call. To undo: re-run with the previous quantity.
