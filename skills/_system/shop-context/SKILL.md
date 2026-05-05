---
id: _system.shop-context
name: shop-context
title: Load shop context
description: Load the connected store's profile (name, currency, timezone, plan, country) so other skills can tailor output to it.
audience: contributor
roles: []
category: _system
risk: read
mcp_tools:
  - get-shop-info
graphql:
  query: false
  mutation: false
triggers:
  - "(internal) load shop context"
  - "(internal) which store am I on"
inputs: []
outputs:
  summary: |
    A small object with name, domain, currency, timezone, country, plan, and a
    short `pretty` line ("example-shop · USD · America/Denver · Shopify plan").
status: stable
version: 1.0.0
---

## Purpose

Almost every other skill needs to know which store it's operating on. This primitive standardizes that lookup so domain skills don't each write their own header line.

## When to use

- As step 1 of any domain skill that reads or writes.
- Whenever a write-skill needs to surface the store name in a confirmation prompt.
- Whenever a read-skill needs currency or timezone for formatting (prices, dates).

## Workflow

1. Call `get-shop-info`.
2. Format the result into a compact object:
   ```
   { name, domain, email_domain, currency, timezone, country, plan, pretty }
   ```
   where `pretty = "<name> · <currency> · <timezone> · <plan> plan"`.
3. Return the object to the caller. Domain skills surface the `pretty` line in their preview/confirm output so the merchant always knows which store is involved.

## Output format

Internal — consumed by other skills. Domain skills present `pretty` to merchants.

## Edge cases

- **Multi-store sessions.** If the merchant just ran `switch-shop`, the next `get-shop-info` may prompt for re-authorization. That's expected behavior.
- **Plan-tier limits.** Some skills limit themselves on lower plans (e.g., `analytics.cohort-retention` requires Shopify or higher). The `plan` field lets domain skills check.
- **Email domain leakage.** `get-shop-info` returns the merchant's `email`. Skills must not log or echo this anywhere — strip it before returning. The output object should expose `email_domain` only (everything after `@`), not the full address.

## Examples

- [`examples/basic.md`](examples/basic.md)
