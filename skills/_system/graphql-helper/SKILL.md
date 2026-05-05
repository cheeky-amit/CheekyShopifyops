---
id: _system.graphql-helper
name: graphql-helper
title: Run a validated Admin GraphQL operation
description: Wraps the schema → docs → validate → execute workflow so domain skills can call GraphQL safely without re-implementing the validation chain.
audience: contributor
roles: []
category: _system
risk: read
mcp_tools:
  - graphql_schema
  - search_docs_chunks
  - validate_graphql_codeblocks
  - graphql_query
  - graphql_mutation
graphql:
  query: true
  mutation: true
triggers:
  - "(internal) run a graphql query"
  - "(internal) run a graphql mutation"
inputs:
  - name: operation
    type: string
    description: The GraphQL operation string (raw, not markdown-fenced).
  - name: variables
    type: object
    default: {}
  - name: kind
    type: string
    description: "'query' or 'mutation'."
  - name: schema_starting_type
    type: string
    description: "Type to introspect first (e.g. 'Mutation', 'QueryRoot', 'Product'). Used by callers that don't yet have an operation drafted."
outputs:
  summary: |
    The GraphQL response on success. On failure: a structured error indicating
    which step failed (schema lookup, validation, execution) and the offending
    fields/types.
status: stable
version: 1.0.0
---

## Purpose

Centralizes the four-step Admin GraphQL workflow this repo requires: schema lookup → docs lookup → validate → execute. Domain skills that need GraphQL call this primitive instead of repeating the chain (and, sometimes, skipping validation by mistake).

## When to use

- Any time a domain skill needs data not covered by direct typed tools (metafields, metaobjects, pages, blogs, markets, translations, publications, discount nodes, etc.).
- Any time a domain skill needs a mutation not covered by direct tools (e.g., updating customer marketing consent, ending a discount, writing a metafield).

## When NOT to use

- A direct tool exists. Use the direct tool — it has structured output and a UI widget.
- The merchant just wants raw GraphQL access. They should use `graphql_query` / `graphql_mutation` directly; this primitive is for skill authors.

## Workflow

0. **Context note.** This primitive is called from a domain skill that has already loaded `_system.shop-context` (which exposes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, `needs_onboarding`, `onboarding_skipped`). The caller — not this primitive — is responsible for the onboarding handoff. This helper itself is also called *by* `_system.shop-context` to read the four `merchant_ai.*` metafields; that's the one place the helper runs without an upstream context, by design.
1. **Schema first.** If the caller didn't provide an operation, call `graphql_schema(<schema_starting_type>)` to enumerate. For mutations, always start at `Mutation` and grep for the right name. For queries on a known type, start at the type itself.
2. **Docs if needed.** If field semantics aren't obvious from the schema, call `search_docs_chunks` with a query like `"<TypeName> <field> example"`.
3. **Validate.** Call `validate_graphql_codeblocks` with the operation string. If validation fails, fix the operation and re-validate. Do not proceed to execute on a validation failure.
4. **Execute.**
   - `kind: 'query'` → call `graphql_query` with the operation and variables. Pagination: include `pageInfo { hasNextPage endCursor }`. Pass `endCursor` as the `after` argument for the next page.
   - `kind: 'mutation'` → call `graphql_mutation`. The host will prompt the merchant for confirmation; the helper does not pre-confirm.
5. **Surface result.** Return the response data to the caller.

## Output format

Internal. Domain skills decide how to surface the data to merchants.

## Safety

- Validation is **never** skipped. If the caller hands in an operation, it must pass `validate_graphql_codeblocks` before execution. No exceptions, including "I'm sure it's right."
- For mutations, this primitive does not replace the host-level confirmation prompt — both must be honored.
- **Blocked mutations** (refunds, gift cards, staff management, theme deletion/publish, live theme writes) will be refused by the MCP. Callers must not retry on a block error; they should surface the limitation to the merchant and direct them to Shopify admin.

## Edge cases

- **Hallucinated fields** in the operation → validation catches it. Return the validation error to the caller.
- **Pagination cap.** Cursor pagination is the only safe way to bulk-read; never use `first: 50` and assume you got everything. Check `hasNextPage`.
- **Throttling.** GraphQL responses include a `throttleStatus`. Helpers that paginate aggressively should respect throttle hints — back off if the bucket is near empty.

## Examples

- [`examples/basic.md`](examples/basic.md) — query a metafield, then update one (the canonical "no direct tool" case).
