# Architecture

## How skills compose

Two layers:

**Primitives** — internal building blocks. Not invoked directly by merchants. Other skills reference them.

```
primitives/
  shop-context/        # Loads shop profile (currency, plan, timezone). Almost every other skill calls this first.
  graphql-helper/      # Wraps schema → docs → validate → execute for any GraphQL hop.
  safe-write/          # Preview/diff/confirm/log/rollback scaffolding. Required by every write skill.
  cohort-builder/      # Reusable patterns for building product/customer cohorts via search syntax.
  data-extraction/     # Pagination-aware bulk read with the 50-per-call cap baked in.
```

**Domain skills** — the things merchants actually ask for. Composed from primitives.

```
catalog/         orders/         analytics/       storefront/
inventory/       customers/      retention/       reporting/
                 promotions/                       meta/wiring/
```

## Why this split

Primitives are the **safety surface**. By centralizing preview/confirm/execute in `safe-write`, every write skill inherits the same contract. Authors focus on what to change, not on how to wrap it safely. Reviewers spot deviations from the contract immediately.

Domain skills are organized by **role intent**, not by Shopify resource. A "weekly merchant brief" skill spans sales + orders + inventory + customers — it lives in `reporting/`, not split across four folders.

## Directly-vs-GraphQL decision tree

Every workflow step asks:

```
Does a typed direct tool cover this?
  yes → use it (better UX, structured output widget)
  no  → use graphql_query / graphql_mutation, following the validation workflow
```

The full GraphQL workflow:

```
graphql_schema(<type>)
  → search_docs_chunks (if examples needed)
  → construct operation
  → validate_graphql_codeblocks
  → graphql_query / graphql_mutation
```

Skills must not skip validation. The `primitives/graphql-helper` skill encodes this so domain skills don't have to repeat it.

## Merchant-facing language

Skills speak in plain language. Tool names never appear in merchant-facing output. Compare:

❌ "I'll call `search_products` with `status:active AND inventory_total:0`."
✅ "Let me find products that are listed for sale but have no stock right now."

Internal skill body can name tools (it's instructions for Claude). Output to the merchant should not.

## Multi-store

`switch-shop` is supported. Skills should call `primitives/shop-context` first so they're aware which store they're operating on, and surface the store name in any preview before a write.

## Where the safety contract lives

[`docs/safety.md`](safety.md) is canonical. The `primitives/safe-write` skill operationalizes it. `CLAUDE.md` enforces it as a hard rule. Three places, one source of truth — when changing the contract, update all three.
