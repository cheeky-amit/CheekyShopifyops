# cohort-builder

**Internal building block.** Standardizes how merchant-typed filters get translated into Shopify search syntax for products, customers, and orders.

## Used by

Any domain skill that accepts a filter input — most catalog, customer, order, retention, and reporting skills.

## What it does

Takes a free-text or named cohort and returns:

- `filter` — a valid Shopify search query string.
- `label` — human-readable for confirmation prompts.
- `named_cohort` — true if the input matched a catalog entry.

## Why this exists

Shopify search syntax is field-sensitive. Pass an unknown field and it silently returns *all records* (footgun). Bare-name customer searches match against notes, tags, and addresses, which surfaces wrong people. The primitive bakes those guardrails in once and every domain skill inherits them.

## Adding named cohorts

Edit the catalog in `SKILL.md` and add a row. Include rationale in the PR. Don't add aliases for cohorts the merchant can express directly with one filter — only add when the named cohort either composes multiple filters or computes a date.
