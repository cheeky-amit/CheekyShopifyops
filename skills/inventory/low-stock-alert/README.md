# Low-stock alert

Products at or below a stock threshold, ranked by recent sales velocity (fast movers first).

> "What's running low?"
> "Low-stock alert."
> "What do I need to reorder?"

## Inputs

- `threshold` (default 5)
- `cohort` (default all)
- `include_out_of_stock` (default false — already past low)

## Why "ranked by recent sales"

Running low on a slow mover is fine. Running low on a fast mover is a fire. The skill ranks by velocity so the fires are at the top.

## What this skill won't do

- Place a reorder. No supplier/3PL integration in this bundle. See `meta.wiring` for combining with other MCPs.
