# safe-write (primitive)

**Internal building block.** The five-step contract every write skill must compose with:

```
read → diff → confirm → CAS-or-recheck → log + rollback note
```

## Why this exists

Without this primitive, every write skill would re-implement the safety pattern slightly differently. With it, reviewers can verify a new skill is safe by checking that it composes with `safe-write` and reads from the same diff/confirm/rollback shape.

This is the operational form of [`docs/safety.md`](../../../docs/safety.md). When the contract changes, both files change together.

## Used by

Every skill with `risk: write` or `risk: bulk-write`:

- `inventory.cycle-count`
- `inventory.receive-shipment`
- `promotions.discount-create`
- `promotions.collection-sale`
- `catalog.product-status-audit`
- `catalog.tag-cleanup`
- `catalog.bulk-status-sweep`

## What callers must provide

- A `read_fn` that returns the current state.
- A `propose_fn` that turns current state into a proposed change set with reasons.
- A `write_fn` that executes the approved change set.
- A `rollback_doc` string the run log embeds.

The primitive provides the diff render, confirm prompt, drift re-check, and run log.
