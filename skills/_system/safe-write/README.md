# safe-write

**Internal building block.** Not invoked directly by merchants. Used by other skills.

The five-step contract every write skill composes with: `read → diff → confirm → CAS-or-recheck → log + rollback note`. Without this, every write skill would re-invent the safety pattern slightly differently. With it, a reviewer can verify a new write skill is safe by checking that it composes with `safe-write` and reads from the same diff/confirm/rollback shape.

## Used by

Every skill with `risk: write` or `risk: bulk-write`:

- `inventory.cycle-count`, `inventory.receive-shipment`
- `promotions.discount-create`, `promotions.collection-sale`
- `catalog.product-status-audit`, `catalog.tag-cleanup`, `catalog.bulk-status-sweep`
- `onboarding.first-run`, `onboarding.refresh-profile`

## What it does

- Renders a human-readable diff from current state and the proposed change set.
- Drives the confirm prompt and parses the merchant's "yes" / "edit" / "no."
- Re-checks state right before execute; uses compare-and-swap where the underlying tool exposes it (e.g. `set-inventory.compareQuantity`) and a re-read otherwise.
- Writes a run log with the previous values so undo is one ask.
- Embeds the caller-supplied rollback note in the log.

## What callers must provide

- A `read_fn` that returns the current state.
- A `propose_fn` that turns current state into a proposed change set with reasons.
- A `write_fn` that executes the approved change set.
- A `rollback_doc` string the run log embeds.

## See also

- [SKILL.md](SKILL.md) — full agent-facing instructions.
- [`docs/contributors/safety-contract.md`](../../../docs/contributors/safety-contract.md) — narrative version of the same contract. The two files change together.
