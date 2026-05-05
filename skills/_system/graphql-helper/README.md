# graphql-helper

**Internal building block.** Skill authors use this so they don't have to re-implement the schema → docs → validate → execute chain every time.

The repo's hard rule: **no `graphql_query` / `graphql_mutation` call may skip validation**. This primitive enforces that.

## Used by

Any domain skill that needs to access a Shopify resource not covered by a direct tool — metafields, metaobjects, pages, blogs, markets, publications, translations, discount nodes (for `promotions.discount-audit`), customer marketing consent, etc.

## What it does

```
schema lookup → docs lookup (if needed) → validate → execute
```

If validation fails, the skill returns the validation errors to the caller and refuses to execute. Callers fix the operation and call again.

## Blocked operations

The MCP refuses some mutations: refunds, gift card writes, staff management, theme deletion, theme publishing, and writes to the live (MAIN) theme. This primitive surfaces the block error and the caller surfaces it to the merchant — pointing to the Shopify admin path instead.
