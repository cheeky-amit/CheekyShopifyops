# graphql-helper

**Internal building block.** Not invoked directly by merchants. Used by other skills.

Wraps the schema → docs → validate → execute chain so no skill has to re-implement it. Enforces the repo's hard rule: no `graphql_query` or `graphql_mutation` call may skip validation.

## Used by

Any domain skill that needs to reach a Shopify resource without a dedicated MCP tool — metafields, metaobjects, pages, blogs, markets, publications, translations, discount nodes, customer marketing consent, and similar.

## What it does

- Looks up the relevant schema fragment.
- Pulls related docs chunks if the operation needs context.
- Validates the operation before any execute.
- Returns validation errors to the caller and refuses to execute if validation fails.
- Surfaces blocked-mutation errors (refunds, gift card writes, staff management, theme deletion / publishing, writes to the live theme) so the caller can route the merchant to the Shopify admin UI instead.

## See also

- [SKILL.md](SKILL.md) — full agent-facing instructions and the validation contract.
- [`_system/safe-write`](../safe-write/) — the write-side scaffolding most mutation callers compose with.
