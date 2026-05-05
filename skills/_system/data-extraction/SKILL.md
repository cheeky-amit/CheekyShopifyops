---
id: _system.data-extraction
name: data-extraction
title: Pagination-aware bulk read
description: Iterate cursor-based listings (search_products, list-orders, list-customers, search_collections, GraphQL) without losing records to the 50-per-call cap.
audience: contributor
roles: []
category: _system
risk: read
mcp_tools:
  - search_products
  - search_collections
  - list-orders
  - list-customers
  - graphql_query
graphql:
  query: true
  mutation: false
triggers:
  - "(internal) bulk read"
  - "(internal) paginate through results"
inputs:
  - name: tool
    type: string
    description: "search_products | search_collections | list-orders | list-customers | graphql_query"
  - name: filter
    type: string
    description: "Filter string from _system.cohort-builder, or a GraphQL operation."
  - name: page_size
    type: integer
    default: 50
    description: "1–50. Default to 50 for fewest round-trips."
  - name: cap
    type: integer
    default: 500
    description: "Max records this skill will pull. Stops after `cap` even if more exist."
  - name: progress_fn
    type: function
    description: "Optional callback: (records_so_far, has_next) → void. For long runs, surface progress to the merchant."
outputs:
  summary: |
    { records: [...], total_fetched, hit_cap: bool, hit_end: bool, last_cursor }.
status: stable
version: 1.0.0
---

## Purpose

Direct list/search tools cap at 50 records per call. Skills that audit, report, or bulk-update need to read more than that. Without a primitive, every skill writes its own pagination loop and most do it slightly wrong (forget `hasNextPage`, forget the cap, forget to surface partial progress).

## When to use

- Any skill that needs more than one page of results.
- Any skill that needs deterministic behavior at the boundary (cap reached, end reached).

## When NOT to use

- The skill needs only one page (`first: 10`). Direct call is simpler.
- The skill needs streaming aggregates (e.g., real-time totals). Different pattern; out of scope.

## Workflow

0. **Context note.** This primitive is called from a domain skill that has already loaded `_system.shop-context` (which exposes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, `needs_onboarding`, `onboarding_skipped`). The caller — not this primitive — is responsible for the onboarding handoff. Callers may scale `cap` based on `store.stage` (typically 500 baseline; up to 2000 on `1m+`).
1. Initialize: `cursor = null`, `records = []`, `iters = 0`.
2. Loop:
   - Call `tool` with `first: page_size` and `after: cursor`.
   - For GraphQL, the query must include `pageInfo { hasNextPage endCursor }` somewhere relevant.
   - Append page records to `records`.
   - If `progress_fn` provided, call it with current state.
   - If `records.length >= cap`, stop with `hit_cap: true`. Truncate to `cap`.
   - If `pageInfo.hasNextPage == false`, stop with `hit_end: true`.
   - Else `cursor = pageInfo.endCursor; iters += 1`. Sanity check: if `iters > cap / page_size + 5`, abort with `runaway loop` error.
3. Return `{ records, total_fetched, hit_cap, hit_end, last_cursor }`.

## Output format

Internal. Caller decides how to handle `hit_cap`:
- For audit skills: surface to merchant ("scanned the first 500 of ~860 — narrow your cohort or run per-vendor").
- For bulk-update skills: must surface — they need merchant approval to operate on a partial set.

## Edge cases

- **Empty result.** `records: [], hit_end: true`. Caller handles; doesn't error.
- **Page size 0.** Reject input.
- **Throttling on GraphQL.** Caller backs off if response indicates near-empty bucket. This primitive surfaces throttle info but does not auto-backoff (different policy per skill).
- **Cursor invalidation.** Rare, but if Shopify returns an error about a stale cursor, the primitive aborts the loop and returns what it has so far with a warning. Caller decides whether to retry from scratch.

## Examples

- [`examples/basic.md`](examples/basic.md)
