---
id: catalog.product-status-audit
name: product-status-audit
title: Audit and fix product statuses
description: Find products in the wrong status (live with no stock, draft with sales, archived but linked) and propose fixes.
audience: merchant
roles: [merchandiser, founder, operations]
category: catalog
risk: bulk-write
mcp_tools:
  - get-shop-info
  - search_products
  - get-product
  - search_collections
  - get-collection
  - bulk-update-product-status
graphql:
  query: false
  mutation: false
triggers:
  - "audit my products"
  - "audit product statuses"
  - "find broken products"
  - "which products are in the wrong status"
  - "find products that are live but out of stock"
  - "find drafts that should be live"
  - "find archived products that are still linked"
inputs:
  - name: dry_run
    type: boolean
    default: true
    description: When true, only preview changes — never call bulk-update-product-status.
  - name: cohort
    type: string
    default: "all"
    description: |
      Limit the audit to a subset using Shopify search syntax — e.g. "vendor:Acme",
      "tag:summer-sale", "product_type:Apparel". Free-text accepted. Default audits everything.
  - name: archive_no_sales_days
    type: integer
    default: 90
    description: How many days of zero sales before suggesting an active no-stock product be archived.
outputs:
  summary: |
    Bullet list of products with current → proposed status and a one-line rationale per product,
    plus a count of changes by direction (e.g. "10 → ARCHIVED, 3 → ACTIVE, 2 → DRAFT").
safety:
  blocks: []
  requires_confirm: true
  rollback: |
    The skill emits a before/after run log. To undo: invoke this skill again with the inverse mapping
    (set each product back to its previous status). bulk-update-product-status accepts up to 50 product IDs
    at a time, so a 200-product change requires four rollback calls.
status: stable
version: 1.0.0
---

## Purpose

Stores accumulate product-status drift over time. Some products that should be selling are stuck in `DRAFT`. Others are live (`ACTIVE`) but have no stock and haven't sold in months. Some are archived but still appear in active collections, breaking the storefront. This skill finds those drifted products and proposes a fix the merchant can approve.

## When to use

- Quarterly catalog cleanup.
- Before a launch, sale, or major theme change.
- Whenever the merchant says "the storefront is messy" or "we have orphan products."
- After a bulk import (CSV, app sync) when status fields may not have been set correctly.

## When NOT to use

- The merchant just wants to change one product's status. Use `update-product` directly.
- The merchant wants to change status for a known list of product IDs. Use `bulk-update-product-status` directly.
- The merchant wants to find products missing description, image, or tags. Use `catalog.missing-content` instead.
- The merchant wants to find duplicate products. Use `catalog.duplicate-detector` instead.

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity. This is bulk-write — the strict confirm in step 5 always applies regardless of `write_defaults`. Surface `shop.pretty` to the merchant — every preview must say which store this is for.

1. (shop context already loaded above — store name and pretty line are available for headers.)

2. **Build the audit cohort.**
   - If `cohort` input is provided, use it as the base filter for `search_products`.
   - Otherwise, audit everything (no base filter).

3. **Find candidates in three buckets.** Run three `search_products` queries (paginated) over the cohort:

   - **Bucket A — `ACTIVE` with no inventory and no recent sales.**
     Filter: `status:active AND inventory_total:0`.
     Then for each result, check `run-analytics-query`:
     ```
     FROM sales SHOW orders WHERE product_id = '<gid>' SINCE -<archive_no_sales_days>d UNTIL today
     ```
     If `orders == 0`, propose `ACTIVE → ARCHIVED`.
     If `orders > 0`, leave alone (recent sales mean it's about to be restocked).

   - **Bucket B — `DRAFT` that look ready to sell.**
     Filter: `status:draft`.
     For each, check: variants have prices, inventory_total > 0, at least one image, in at least one published collection.
     If all true, propose `DRAFT → ACTIVE`.

   - **Bucket C — `ARCHIVED` still in active collections.**
     Filter: `status:archived`.
     For each, call `get-product` to find its collections. If any collection is published, propose `ARCHIVED → DRAFT` (the merchant can choose to fully delete or revive).

4. **Compose the diff.**
   Group results by direction. Show the merchant:
   ```
   Audit on <store-name> · cohort: <cohort or "everything">

   Proposed: 12 changes

     → ARCHIVE  (no stock, no sales in <N> days)
       • Product A     [ID …001]   was: ACTIVE
       • Product B     [ID …002]   was: ACTIVE
       …

     → ACTIVATE  (looks ready to sell)
       • Product C     [ID …003]   was: DRAFT  (priced $XX.XX, 24 in stock)
       …

     → MOVE TO DRAFT  (archived but still in active collections)
       • Product D     [ID …004]   was: ARCHIVED  (in collection: "Featured")
       …

   Type "yes — apply these <N>" to apply (the count is required so a casual "ok" can never trigger a bulk run).
   Or: "modify" to adjust, "no" to abort.
   Preview-only is currently: <on|off>.
   ```
   No raw GIDs in body unless the merchant asks for them. No real product names — the **runtime** will show real names; this skill's *examples* in this repo only ever use placeholders.

5. **Confirm.** Wait for explicit confirmation. If `dry_run = true` (default), tell the merchant the preview-only mode is on and they need to opt out (e.g., "apply for real") to actually write. The bulk-write confirmation must include the exact change count — accept only `yes — apply these <N>` where `<N>` matches the proposed count from step 4. A bare "yes" or "ok" is rejected; ask the merchant to repeat with the count. This applies regardless of `write_defaults`.

6. **Execute.**
   - If `dry_run = true`, stop here. Output the run log only.
   - If `dry_run = false` and the merchant confirmed, batch product IDs by target status (`ACTIVE`, `DRAFT`, `ARCHIVED`).
   - For each batch, call `bulk-update-product-status` with up to 50 IDs.
   - Re-read each updated product (or use the tool's response) to verify the status applied.

7. **Log and rollback note.** Output a structured before/after log:
   ```
   Run: catalog.product-status-audit @ <iso-timestamp>
   Store: <store>
   Cohort: <cohort>
   Changes applied: 12

     gid://shopify/Product/000   ACTIVE → ARCHIVED
     gid://shopify/Product/001   ACTIVE → ARCHIVED
     …
   ```
   Tell the merchant: "Save this if you might want to undo. To revert, ask me to set those products back to their previous status."

## Output format

What the merchant sees:

- A 1-line summary: `12 products to change · 10 archive · 1 activate · 1 to draft`.
- The grouped diff (above).
- A confirmation prompt.
- After execution: the run log, plus a one-line status confirmation from each batch (`Batch 1/3 applied: 50 products → ARCHIVED`).

What the merchant does NOT see:
- Raw tool calls.
- Raw GraphQL.
- Internal cohort query syntax (unless they ask).

## Safety

- **Default dry-run.** Without `dry_run: false`, no writes happen. Even after a confirm, `dry_run: true` short-circuits.
- **Compare-and-swap is not available** for `bulk-update-product-status`. Mitigate by re-running the bucket queries immediately before execution and aborting if any candidate's status has drifted from what was previewed (someone else changed it mid-flight).
- **Cap of 50 per call.** Skills must batch and report partial-success counts. If batch 2 of 3 fails, the run log shows batches 1 succeeded, 2 failed, 3 not attempted — and the rollback only needs to address what was actually applied.
- **No deletion.** This skill never deletes products. `ARCHIVED → DELETED` requires a separate explicit ask, and even then this skill won't do it; deletion is destructive and merits its own workflow.
- **Rollback path.** Inverse mapping via `bulk-update-product-status`. The skill outputs the inverse map alongside the success log so the merchant can paste it back if needed.

## Edge cases

- **Empty cohort.** Filter returned 0 products → "Nothing to audit in cohort `<cohort>`. Try `cohort: 'all'`."
- **Pagination cap.** If any of the three bucket queries returns `hasNextPage: true`, continue paginating. Cap total candidates at 500 per run; if more, tell the merchant and ask them to narrow the cohort.
- **Sales lookup partial.** If `run-analytics-query` fails for one product (e.g., timeout), exclude that product from the archive bucket and note it. Do not assume zero sales.
- **Inventory tracking off.** A product with `inventoryItem.tracked: false` always reports `inventory_total: 0` even when it's effectively in stock. Detect this via `get-product` and exclude such products from the archive bucket. Note them as "untracked — review manually."
- **Multi-location stock.** `inventory_total` aggregates all locations. A product can be `inventory_total: 0` because it's at a closed warehouse. Surface a footnote suggesting the merchant check `inventory.snapshot` if results look surprising.
- **Discount-only products / gift cards / digital downloads.** Products with no inventory by design. Detect via product type (`Gift Card`, common digital tags) and skip them.
- **Active product in a draft collection.** Bucket C only flags archived products in *published* collections. Verify `published_status:published` when fetching the collection list.
- **Race with another tool.** If between preview and execute, the merchant runs another bulk-status tool, the re-check in step 6 will catch drift on candidates and skip those that no longer match the original bucket.

## Examples

- [`examples/basic.md`](examples/basic.md) — typical run, dry-run, then live execute.
- [`examples/edge-cases.md`](examples/edge-cases.md) — untracked inventory, paging cap, and rollback.
