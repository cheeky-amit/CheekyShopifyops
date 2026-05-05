---
id: primitives.safe-write
name: safe-write
title: Preview, confirm, execute, log
description: The five-step write contract every mutating skill must compose with — read current state, show diff, confirm, write with CAS where supported, log before/after.
audience: contributor
roles: []
category: primitives
risk: read
mcp_tools: []
graphql:
  query: false
  mutation: false
triggers:
  - "(internal) wrap a write operation"
inputs:
  - name: read_fn
    type: function
    description: A no-arg function returning the current state of the records to be modified.
  - name: propose_fn
    type: function
    description: A function (current → proposed) that returns the change set.
  - name: write_fn
    type: function
    description: A function that executes the write, given the approved change set.
  - name: rollback_doc
    type: string
    description: Plain-language explanation of how to undo this change.
outputs:
  summary: |
    A run object: { changes_proposed, changes_applied, before, after, rollback_instructions }.
status: stable
version: 1.0.0
---

## Purpose

The repo's safety contract is "read → diff → confirm → CAS-write → log → rollback note." This primitive is the contract in concrete form. Every domain skill with `risk: write` or `risk: bulk-write` composes with this primitive.

Reviewers verify compliance by checking that write skills reference `primitives.safe-write` in their workflow.

## When to use

- Any skill that mutates Shopify state. No exceptions.
- Wrapping a single-record write (e.g., `update-product` for one product).
- Wrapping a bulk write (e.g., `bulk-update-product-status` over 50 products).

## When NOT to use

- Read-only skills. They have no diff, no confirm, no rollback — `safe-write` adds friction with no payoff.
- The host already prompted the merchant for confirmation on its own (e.g., the `graphql_mutation` host-level prompt). The host prompt does not replace `safe-write` — it's a second checkpoint, not a substitute. The skill still needs to show its own merchant-readable diff.

## Workflow

1. **Read.** Call `read_fn`. Capture `before`. If `before` is empty (no records match the cohort), return `{ changes_proposed: 0, ... }` and stop — never confirm or write on an empty diff.
2. **Propose.** Call `propose_fn(before)`. The result is a change set: list of `{ id, current, proposed, reason }`.
3. **Diff render.** Format the change set into a merchant-readable preview, grouped by direction (e.g., "→ ARCHIVE", "→ ACTIVATE"). Show counts. Show real names (at runtime) but never expose internal tool names. Surface the store's `pretty` line from `primitives.shop-context` so the merchant always knows which store this is.
4. **Confirm.** Prompt explicitly:
   - For single-record writes: accept "yes / proceed / confirm / apply."
   - For bulk writes (`risk: bulk-write`): require unambiguous confirmation. "ok" alone is not enough.
   - "modify" → return to step 2 with the merchant's adjustment.
   - "no / stop / abort" → write nothing, log nothing, return.
5. **Re-check before write (CAS or read-back).**
   - If the caller's `write_fn` uses a tool that supports CAS (e.g., `set-inventory` `compareQuantity`), pass through the `before` values.
   - Otherwise, re-read the records and detect drift: any record whose state has changed since `before` is removed from the change set with a "skipped — drifted" note.
6. **Execute.** Call `write_fn(approved_change_set)`. Capture per-record results. For bulk operations, surface batch-level success/failure.
7. **Log.** Compose the run log:
   ```
   Run: <skill-id> @ <iso-timestamp>
   Store: <pretty>
   Changes proposed: N
   Changes applied:  M  (M ≤ N — drift-skipped items not counted)
   Failed:           K

     <id>  <field>  <before> → <after>
     …

   Rollback: <rollback_doc string>
   ```
8. **Surface to merchant.** Show the run log, plus the line: "Save this if you want to undo."

## Output format

Internal — caller (the domain skill) handles merchant-facing rendering. The primitive returns a structured run object the caller can include verbatim in its output.

## Edge cases

- **Empty diff.** Stop immediately after step 1. Don't waste the merchant's attention on an empty confirm.
- **Mid-flight drift.** Caught in step 5. Drifted items are skipped; the run log notes them.
- **Partial failure.** If the write tool returns success for some records and failure for others, the log distinguishes applied/failed/not-attempted.
- **Tool unavailable / network error.** Surfaces as a step-6 failure. Log captures what was attempted; the merchant gets a clean "X of N succeeded" report.
- **Merchant says "yes" to a bulk write that exceeds the tool's per-call cap.** The caller batches; this primitive doesn't enforce cap awareness, but most callers will batch via `_system/data-extraction` patterns.

## Examples

- [`examples/basic.md`](examples/basic.md)
