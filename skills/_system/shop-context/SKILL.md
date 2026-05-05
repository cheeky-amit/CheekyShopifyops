---
id: _system.shop-context
name: shop-context
title: Load shop context (with merchant profile)
description: Load the connected store's profile plus the merchant's saved operator/store/rituals/onboarding profile so other skills can tailor output and decide whether to onboard.
audience: contributor
roles: []
category: _system
risk: read
mcp_tools:
  - get-shop-info
  - graphql_query
  - graphql_schema
  - validate_graphql_codeblocks
graphql:
  query: true
  mutation: false
triggers:
  - "(internal) load shop context"
  - "(internal) which store am I on"
  - "(internal) read merchant profile"
inputs: []
outputs:
  summary: |
    Object with `shop` (name/currency/timezone/plan), plus saved profile pieces
    (`operator`, `store`, `rituals`, `onboarding_state`) and the derived flags
    `needs_onboarding` and `onboarding_skipped`. Skills branch on those flags.
status: stable
version: 2.0.0
---

## Purpose

Almost every other skill needs two things: which store am I on, and who am I talking to / how should I behave. This primitive standardizes both lookups so domain skills don't each repeat them.

The merchant profile lives in shop-level metafields under namespace `merchant_ai` (see `docs/contributors/profile-schema.md`). On first run for a store, `onboarding_state` is null and callers should hand off to `onboarding.first-run`. After that, downstream skills tune their voice, write-confirm strictness, and stage-aware defaults from the saved profile.

## When to use

- As step 0 of any domain skill that reads or writes.
- Whenever a write-skill needs to surface the store name in a confirmation prompt.
- Whenever a read-skill needs currency or timezone for formatting (prices, dates).
- Whenever a skill wants to scale defaults by store stage (low-stock thresholds, top-N caps, etc.).

## Workflow

1. Call `get-shop-info` for the basic profile (name, domain, email, currency, timezone, country, plan).
2. Read the four `merchant_ai.*` shop metafields with one GraphQL query (validate via `_system.graphql-helper`):
   ```graphql
   query ShopProfile {
     shop {
       id
       name
       primaryDomain { url }
       email
       currencyCode
       ianaTimezone
       billingAddress { country }
       plan { displayName }
       metafield_operator: metafield(namespace: "merchant_ai", key: "operator_profile") { value }
       metafield_store: metafield(namespace: "merchant_ai", key: "store_profile") { value }
       metafield_rituals: metafield(namespace: "merchant_ai", key: "rituals") { value }
       metafield_onboarding: metafield(namespace: "merchant_ai", key: "onboarding_state") { value }
     }
   }
   ```
   Each metafield value is a JSON string (or null). Parse each one with safe JSON parsing — on parse failure treat that metafield as null and proceed.
3. Compose the merged context object:
   ```
   {
     shop: {
       name, domain, email_domain, currency, timezone, country, plan, pretty
     },
     operator:         { name, role, voice, write_defaults }              | null,
     store:            { stage, model, discipline, last_refreshed }       | null,
     rituals:          { monday_brief, weekly_brief_day }                 | null,
     onboarding_state: { completed_at, declined_at, version }             | null,
     needs_onboarding: bool,
     onboarding_skipped: bool
   }
   ```
   `shop.pretty` is `"<name> · <currency> · <timezone> · <plan> plan"`.
   `shop.email_domain` is the part after `@` only — never expose the full email.
4. Derive `needs_onboarding` and `onboarding_skipped`:
   - If `onboarding_state` is null OR (`completed_at` is null AND `declined_at` is null) → `needs_onboarding: true`, `onboarding_skipped: false`.
   - If `declined_at` is set AND was within the last 30 days → `needs_onboarding: false`, `onboarding_skipped: true`. Caller should NOT auto-trigger onboarding; should use defaults.
   - If `declined_at` is set but more than 30 days old → `needs_onboarding: true`, `onboarding_skipped: false`. Caller may offer onboarding again but should not force.
   - If `completed_at` is set AND `store.last_refreshed` is more than 6 months old → `needs_onboarding: false`, `onboarding_skipped: false`. Caller may suggest a refresh.
   - Otherwise (`completed_at` set, fresh) → `needs_onboarding: false`, `onboarding_skipped: false`.
5. Return the object to the caller. Domain skills surface `shop.pretty` in their preview/confirm output and branch on the profile fields and flags as needed.

## Output format

Internal — consumed by other skills. Domain skills present `shop.pretty` to merchants, and use the profile fields to:

- Pick verbosity (`operator.voice`).
- Pick confirm strictness (`operator.write_defaults` — `a` always preview/always confirm; `b` preview, "do it" implies yes; `c` confirm only on big/bulk).
- Scale defaults (`store.stage`).

## Edge cases

- **Multi-store sessions.** If the merchant just ran `switch-shop`, the next `get-shop-info` may prompt for re-authorization. That's expected; re-run shop-context after switch.
- **First call on a store.** No `merchant_ai.*` metafields exist yet → `needs_onboarding: true`. Caller hands off to `onboarding.first-run`.
- **Plan-tier limits.** Some skills limit themselves on lower plans (e.g. `analytics.cohort-retention` requires Shopify or higher). `shop.plan` lets domain skills check.
- **Email domain leakage.** `get-shop-info` returns the merchant's full `email`. Skills must not log or echo this. Strip it down to `email_domain` before returning.
- **Metafield read fails.** If the GraphQL query errors (permissions, network), treat all four profile metafields as null. `needs_onboarding: true` falls out naturally; the caller can offer onboarding or fall back to in-conversation defaults.
- **Malformed JSON.** A profile metafield with corrupt JSON parses to null for that field only; the rest of the profile is unaffected.
- **Stale profile.** `completed_at` set but `store.last_refreshed` > 6 months → `needs_onboarding` stays false. Callers may surface a one-line "want to refresh your profile?" hint without forcing.

## Examples

- [`examples/basic.md`](examples/basic.md)
