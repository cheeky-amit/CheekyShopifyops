---
id: primitives.cohort-builder
name: cohort-builder
title: Build product, customer, or order cohorts
description: Reusable patterns for building filtered cohorts from Shopify search syntax, so domain skills don't reinvent filter strings.
audience: contributor
roles: []
category: primitives
risk: read
mcp_tools:
  - search_products
  - list-customers
  - list-orders
  - search_collections
graphql:
  query: false
  mutation: false
triggers:
  - "(internal) build a cohort"
  - "(internal) parse a cohort spec"
inputs:
  - name: spec
    type: string
    description: |
      Free-text or structured cohort spec. Examples:
        "all"
        "vendor:Vendor A"
        "tag:summer-sale AND price:>50"
        "customers in Canada with >5 orders"
        "lapsed VIPs"  ← named cohort, see catalog below
  - name: kind
    type: string
    description: "products | customers | orders"
outputs:
  summary: |
    A query string ready to pass to search_products / list-customers / list-orders,
    plus a human-readable label ("VIP customers in Canada") for use in confirmations.
status: stable
version: 1.0.0
---

## Purpose

Every domain skill takes a "cohort" or "filter" input. Without a primitive, each skill writes its own filter parsing — and they drift. This primitive standardizes:

- Pass-through of valid Shopify search syntax (`status:active AND tag:sale`).
- Named cohorts that compile to filter strings ("lapsed VIPs" → `tag:vip AND orders_count:>=3`).
- A human-readable label so the confirmation prompt makes sense.
- Guardrails against commonly-misused filters (e.g., bare-name searches in `list-customers`, which match notes/addresses).

## When to use

- Any domain skill that accepts a cohort/filter input from the merchant.

## When NOT to use

- The skill operates on a known list of IDs (e.g., "archive these 10 product IDs"). No cohort needed.

## Workflow

1. Parse the `spec` input.
2. **Pass-through.** If the spec uses valid Shopify search syntax for the chosen `kind`, return it as-is with a human label.
3. **Named cohort lookup.** If the spec is a named cohort (see catalog below), expand it to its filter string.
4. **Natural-language fallback.** If the spec is plain English ("customers in Canada with >5 orders"), translate using the supported field table for the chosen `kind`. Validate that all referenced fields exist; reject unknown fields with a clear error.
5. **Sanitize.** For customer cohorts: if the spec is a bare name (e.g., "Smith"), force `first_name:Smith OR last_name:Smith` — bare names match notes/addresses/tags.
6. **Return.** `{ filter: "<query string>", label: "<human readable>", kind, named_cohort: bool }`.

## Named cohort catalog

These compile to filter strings. Add new ones via PR; document the rationale.

### Products

| Name | Compiles to | Notes |
|---|---|---|
| `all` | (no filter) | Audit everything. |
| `live` | `status:active` | |
| `live-out-of-stock` | `status:active AND inventory_total:0` | |
| `drafts` | `status:draft` | |
| `archived` | `status:archived` | |
| `untagged` | `tag:''` | Catches products with empty tag list. |
| `gift-cards` | `gift_card:true` | |
| `bundles` | `bundles:true` | |
| `out-of-stock-anywhere` | `out_of_stock_somewhere:true` | At least one location is out. |
| `priced-down` | `is_price_reduced:true` | Has a compareAtPrice > price. |

### Customers

| Name | Compiles to | Notes |
|---|---|---|
| `all` | (no filter) | |
| `vips` | `tag:vip` | Convention; merchants tag VIPs themselves. |
| `subscribers` | `email_marketing_state:subscribed` | |
| `non-subscribers` | `email_marketing_state:not_subscribed` | |
| `lapsed` | `orders_count:>=1 AND updated_at:<'<6mo-ago>'` | "6mo-ago" is computed at skill-call time. |
| `repeat-buyers` | `orders_count:>=2` | |
| `high-value` | `total_spent:>=500` | Threshold parameterized via skill input. |
| `lapsed-vips` | `tag:vip AND orders_count:>=2 AND updated_at:<'<6mo-ago>'` | |

### Orders

| Name | Compiles to | Notes |
|---|---|---|
| `all` | (no filter) | |
| `unfulfilled` | `fulfillment_status:unfulfilled` | |
| `partially-fulfilled` | `fulfillment_status:partial` | |
| `paid-unfulfilled` | `financial_status:paid AND fulfillment_status:unfulfilled` | The fulfillment watchlist baseline. |
| `recent` | `created_at:>'<7d-ago>'` | Last 7 days. |
| `at-risk-stuck` | `fulfillment_status:unfulfilled AND created_at:<'<7d-ago>'` | Unfulfilled for over a week. |

## Edge cases

- **Bare name in customer spec.** Always rewrite to `first_name:X OR last_name:X`. Never pass through.
- **Date math.** "Lapsed" needs an absolute ISO date — the primitive computes it at call time using the shop's timezone (from `primitives.shop-context`).
- **Unknown field name.** Reject with a clear error. Shopify silently ignores unknown fields and returns *everything* — that's a footgun.
- **Mixed kinds.** A spec like "products in collection X" is a `kind: products` cohort with `collection_id:gid://...`. The primitive resolves the collection by name → GID via `search_collections` if needed.

## Examples

- [`examples/basic.md`](examples/basic.md)
