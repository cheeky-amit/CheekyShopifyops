---
id: onboarding.first-run
name: first-run
title: First-run merchant onboarding
description: One-time setup that learns who's running the store and how they want Claude to work, so every other skill can tailor itself.
audience: merchant
roles: [founder, operations, marketing, finance, cx, merchandiser]
category: onboarding
risk: write
mcp_tools:
  - get-shop-info
  - search_products
  - search_collections
  - list-orders
  - list-customers
  - run-analytics-query
  - graphql_query
  - graphql_mutation
  - graphql_schema
  - validate_graphql_codeblocks
graphql:
  query: true
  mutation: true
triggers:
  - "set me up"
  - "onboard me"
  - "first-time setup"
  - "I'm new"
  - "getting started"
  - "help me get started"
  - "(auto-trigger when shop-context returns needs_onboarding: true)"
inputs: []
outputs:
  summary: |
    Shows the inferred store profile, asks 3 quick gap questions plus a ritual question,
    previews the profile, confirms, and saves it under merchant_ai.* shop metafields.
    Or saves a 'declined' marker if the merchant skips.
safety:
  blocks: []
  requires_confirm: true
  rollback: |
    Ask "forget my profile" or use onboarding.refresh-profile to delete or modify
    saved profile metafields. metafieldsDelete on the four merchant_ai.* keys
    fully removes the saved profile.
status: stable
version: 1.0.0
---

## Purpose

Most other skills behave better when they know who's running the store and at what stage. This skill is the one-time intake: it auto-detects what it can from the store itself, asks 3–4 quick questions to fill the gaps, previews what it learned, and saves a small profile to the store's own metafields under namespace `merchant_ai`. Every other skill reads that profile via `_system.shop-context`.

The merchant can always type "skip" and we save a "declined" marker so we don't badger them; downstream skills fall back to safe defaults.

## When to use

- Auto-trigger: `_system.shop-context` returns `needs_onboarding: true` and the user's ask is for some other skill — onboard silently, complete, then resume the original ask.
- On explicit ask: "set me up", "onboard me", "first-time setup".

## When NOT to use

- The merchant already onboarded (`needs_onboarding: false`). If they want changes, route to `onboarding.refresh-profile`.
- The merchant declined within the last 30 days (`onboarding_skipped: true`). Use defaults; respect the decline.
- The merchant just wants a tour of capabilities. Use `onboarding.tour`.

## Workflow

1. **Step 0 — load context.** Call `_system.shop-context`. If `needs_onboarding: false` AND the merchant didn't say "set me up" / "update profile" → exit and tell them they're already set up; offer `onboarding.refresh-profile` if they want changes.

2. **Auto-discovery (read-only sweep, ~5–10 calls).** Build a draft store profile from signals — do NOT save anything yet:
   - `get-shop-info` for plan / currency / timezone / country (already in shop-context).
   - `run-analytics-query` `FROM products SHOW count` for catalog size.
   - `search_collections first:1` for "any collections at all?" (boolean) plus `run-analytics-query` `FROM collections SHOW count` if available.
   - `list-orders first:1` plus ShopifyQL `FROM sales SHOW orders SINCE -30d` for recent volume.
   - `list-customers first:1` for customer base signal.
   - ShopifyQL `FROM sales SHOW returning_customer_rate SINCE -90d` for repeat profile.
   - GraphQL (validate via `_system.graphql-helper`):
     ```graphql
     query Locations { locations(first: 10) { edges { node { id name } } } }
     ```
     for single vs multi location.
   - `search_products first:5` and check each variant's `inventoryItem.tracked` for tracking-discipline signal.

   Compose draft `store` profile:
   ```
   { stage: <inferred from 30d sales: just-launched | under-10k-mo | 10k-100k | 100k-1m | 1m+>,
     model: "<one-line summary inferred from currency/country/catalog/locations>",
     discipline: { tracks_inventory: bool, single_location: bool, has_collections: bool },
     last_refreshed: <now-iso> }
   ```

3. **Show inferred profile and ask 3 gap questions plus 1 ritual question.**

   Show the merchant in plain language what was inferred — currency, plan, catalog size, recent volume, returning rate, locations, tracking discipline — then ask:

   - **(a) Operator identity.**
     "Who am I talking to, and what's your role? (Examples: 'Amit, founder' / 'Sara, ops manager' / 'Joe, marketing')"
   - **(b) Stage.**
     "Roughly what stage is the store in? (just-launched / under-10k-mo / 10k-100k / 100k-1m / 1m+)"
   - **(c) Write defaults.**
     "When you ask me to change something — what's your default?
     (a) Always show me a preview and wait for 'yes' even on small changes.
     (b) Show me a preview, but if I say 'do it' assume yes.
     (c) For big or bulk changes, always confirm. For small ones, just do it."
   - **(d) Ritual.**
     "Want a Monday-morning brief each week? (yes-Monday / yes-other-day / no)"

4. **Skip handling.** If the merchant types "skip" / "use defaults" / declines:
   - Construct `onboarding_state = { completed_at: null, declined_at: <now-iso>, version: "1.0" }`.
   - Leave the other three metafields null/absent.
   - Validate and execute via `_system.graphql-helper`:
     ```graphql
     mutation SaveDecline($metafields: [MetafieldsSetInput!]!) {
       metafieldsSet(metafields: $metafields) {
         metafields { id namespace key value }
         userErrors { field message code }
       }
     }
     ```
     Variables: a single metafield entry, `ownerId: <Shop GID>`, namespace `merchant_ai`, key `onboarding_state`, type `json`, value = JSON-stringified onboarding_state.
   - Tell the merchant: "OK — using defaults (always preview, always confirm). You can run 'set me up' any time."
   - End.

5. **Preview the profile that will be saved.**
   ```
   Profile preview:
     Operator:
       Name:           <answered>
       Role:           <answered>
       Voice:          conversational  (default; we'll learn yours over time)
       Write defaults: <a/b/c → human-readable phrasing>
     Store:
       Stage:          <answered>
       Model:          <inferred from signals>
       Discipline:     <inferred>
       Last refreshed: <now>
     Rituals:
       Weekly brief:   <none / Monday / other-day>
     Onboarding state:
       Completed:      <now>
       Version:        1.0

   Save this to your store under merchant_ai.* metafields?
   Type "yes" to save, "edit" to change something, "no" to skip and use defaults.

   Heads-up: after you say yes, Shopify itself will ask you to confirm the
   save once more — that's normal. It's the host approving the write.
   ```

6. **Confirm.** Wait for "yes" / "edit" / "no". If "edit", loop back to step 5 with the field they want to change. If "no", treat as skip (step 4).

7. **Execute.** Validate and call `metafieldsSet` via `_system.graphql-helper`:
   ```graphql
   mutation SaveProfile($metafields: [MetafieldsSetInput!]!) {
     metafieldsSet(metafields: $metafields) {
       metafields { id namespace key value }
       userErrors { field message code }
     }
   }
   ```
   Variables: 4 metafields, all on the same `ownerId` (the Shop GID), namespace `merchant_ai`, type `json`, with keys and JSON-stringified values:
   - `operator_profile` → `{ name, role, voice: "conversational", write_defaults: "a"|"b"|"c" }`
   - `store_profile` → `{ stage, model, discipline, last_refreshed }`
   - `rituals` → `{ monday_brief: bool, weekly_brief_day: string|null }`
   - `onboarding_state` → `{ completed_at: <now>, declined_at: null, version: "1.0" }`

   The host will prompt the merchant for mutation confirmation — that's a SECOND confirmation on top of step 6. We warned them about this in step 5.

8. **Confirm save.** On `userErrors: []`, say: "Saved. You're all set." Then prompt: "What do you want to work on?"

## Output format

What the merchant sees:

- A short read-back of inferred signals.
- Four questions, one at a time or together (operator's choice).
- A profile preview with their answers and inferred fields filled in.
- A confirmation prompt, followed by the host's own mutation prompt.
- A one-line save confirmation.

## Safety

- **This skill writes the merchant's own profile, not store data.** No products, orders, customers, or inventory are touched.
- **Two confirmations on first run.** Step 6 (this skill's "yes/edit/no") plus the host's mutation prompt for `metafieldsSet`. We warn about both in step 5 so it isn't a surprise.
- **Skip is always valid.** A merchant can decline at any time before step 7. Decline writes only `onboarding_state` with `declined_at`.
- **Mutation blocked / write fails.** If `metafieldsSet` errors (host blocks, network failure, permissions), fall back: "I can't save your profile to your store right now — I'll remember it for this conversation only. Try again next time." Use the in-conversation values for the rest of the session and exit cleanly.
- **Rollback.** Run `onboarding.refresh-profile` and either edit fields or "forget my profile" (which calls `metafieldsDelete` on all four keys).

## Edge cases

- **Merchant skips at any prompt.** Save `declined_at` only (step 4). Use defaults from then on.
- **Merchant edits a field after preview.** Loop back to step 5 with the corrected value. Re-show full preview before re-confirming.
- **Switch-shop mid-flow.** If `switch-shop` fires between steps, abort. Tell the merchant: "Looks like you switched stores — I'll start over once the switch is done." On the next call to `_system.shop-context`, this skill triggers fresh on the new store.
- **Mutation blocked or returns userErrors.** Fall back to in-conversation profile (above). Don't retry silently.
- **Shop ID lookup fails.** The mutation needs the Shop GID. If `get-shop-info` (already done in shop-context) didn't return it, fetch with a tiny `query { shop { id } }` before mutating.
- **Inferred stage and answered stage disagree.** Use the answered value. Note the inferred value in the preview as a sanity check ("we'd have guessed `under-10k-mo`; you said `10k-100k` — going with yours").

## Examples

- [`examples/basic.md`](examples/basic.md) — full run, save profile.
- [`examples/skip.md`](examples/skip.md) — merchant types "skip"; declined marker saved.
