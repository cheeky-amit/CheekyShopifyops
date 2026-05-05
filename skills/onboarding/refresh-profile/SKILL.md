---
id: onboarding.refresh-profile
name: refresh-profile
title: Update or forget the merchant profile
description: Edit any field of the saved merchant profile — or delete it entirely — when the operator, the store, or the rituals change.
audience: merchant
roles: [founder, operations, marketing, finance, cx, merchandiser]
category: onboarding
risk: write
mcp_tools:
  - get-shop-info
  - graphql_query
  - graphql_mutation
  - graphql_schema
  - validate_graphql_codeblocks
graphql:
  query: true
  mutation: true
triggers:
  - "update my profile"
  - "I'm a new operator"
  - "we just hired"
  - "the store changed"
  - "my role changed"
  - "forget my profile"
  - "refresh my profile"
inputs: []
outputs:
  summary: |
    Shows the currently-saved profile, accepts edits to any field (or "forget" to
    delete the entire profile), previews, confirms, and writes changes via
    metafieldsSet (or metafieldsDelete for forget).
safety:
  blocks: []
  requires_confirm: true
  rollback: |
    The skill emits a before/after run log. To undo, run this skill again with
    the previous values. To fully reset, run "forget my profile" then "set me up"
    (onboarding.first-run).
status: stable
version: 1.0.0
---

## Purpose

Profiles change. Operator gets replaced; store moves up a stage; the merchant decides they want fewer prompts. This skill is the one place to edit any field of the saved profile, or to delete it entirely. It's a thin write skill that reads the current profile, accepts edits, previews, confirms, and writes via `metafieldsSet` (or `metafieldsDelete` if the merchant says "forget my profile").

## When to use

- Operator change: "I'm a new operator", "we just hired", "my role changed".
- Stage change: "we crossed 100k", "the store changed".
- Preference change: "stop asking me to confirm small changes" → update `write_defaults`.
- Ritual change: "no more Monday brief".
- Full reset: "forget my profile".

## When NOT to use

- No profile exists yet (`onboarding_state` null). Route to `onboarding.first-run`.
- Merchant just wants a tour of capabilities. Use `onboarding.tour`.

## Workflow

1. **Step 0 — load context.** Call `_system.shop-context`.
   - If `operator`, `store`, `rituals`, and `onboarding_state` are all null → tell the merchant they haven't onboarded yet and suggest `onboarding.first-run`. Stop.
   - Otherwise continue.

2. **Show the current profile** in plain language: operator name/role/voice/write-defaults, store stage/model/discipline/last_refreshed, rituals, onboarding completion date.

3. **Accept edits.**
   - If the merchant said "forget my profile" (or asks to delete during this flow): jump to step 6's delete branch.
   - Otherwise, ask which field(s) to change. Accept any subset of the four metafield groups. Update local copies of the JSON values; do not write yet.
   - Always update `store.last_refreshed` to now if the store profile changed.

4. **Preview** the new profile, formatted exactly like `onboarding.first-run` step 5. Show the diff (`was → now`) for changed fields. Note that the host will prompt for mutation confirmation as a second step.

5. **Confirm.** Wait for "yes" / "edit" / "no". On "edit", loop back to step 3. On "no", abort with "Nothing changed."

6. **Execute.**
   - **Pre-write re-read (round-trip).** Immediately before writing, re-fetch the raw metafield value for each key being changed. Merge the merchant's edits into the raw JSON object so any unknown fields a future v2 might have added survive the round-trip. If the re-read JSON differs from what the merchant saw in the preview, abort (CAS mitigation; see Safety).
   - **Edit branch.** Validate and call `metafieldsSet` via `_system.graphql-helper`. Same mutation shape as first-run, but only with the keys that changed, and with the merged JSON so unknown fields are preserved. (Unchanged metafields can be omitted; `metafieldsSet` is upsert-per-key.)
   - **Forget branch.** Validate and call `metafieldsDelete` via `_system.graphql-helper`:
     ```graphql
     mutation ForgetProfile($metafields: [MetafieldIdentifierInput!]!) {
       metafieldsDelete(metafields: $metafields) {
         deletedMetafields { ownerId namespace key }
         userErrors { field message code }
       }
     }
     ```
     Variables: 4 entries, all `ownerId: <Shop GID>`, namespace `merchant_ai`, keys `operator_profile`, `store_profile`, `rituals`, `onboarding_state`.
     Tell the merchant: "Profile deleted. Next ask will trigger first-run again — type 'skip' if you want to keep using defaults."

7. **Confirm save / delete.** On `userErrors: []`, output a short success line and a before/after run log:
   ```
   Run: onboarding.refresh-profile @ <iso-timestamp>
   Store: <store>
   Changes:
     operator.role:       founder → ops manager
     operator.write_defaults: a → b
   ```
   Tell the merchant: "Save this if you might want to undo. To revert, ask me to set those fields back."

## Output format

What the merchant sees:

- Current profile in plain language.
- A prompt for what to change.
- A diff'd preview, then a confirm prompt, then the host's mutation prompt.
- A short success line and a run log.

## Safety

- **Two confirmations.** This skill's "yes/edit/no" plus the host's mutation prompt for `metafieldsSet` / `metafieldsDelete`. Same as first-run — flag in step 4.
- **Forget is destructive.** "Forget my profile" deletes all four metafields. Confirm explicitly with a separate prompt: "This will delete everything we've saved about you and the store. Are you sure? (yes / no)".
- **Compare-and-swap.** `metafieldsSet` and `metafieldsDelete` don't expose a CAS field. Step 1 re-reads the current metafield value immediately before write. If the parsed values differ from what was shown to the merchant in the preview (someone else updated the profile mid-flight), abort and ask the merchant to refresh.
- **Mutation blocked / write fails.** If `metafieldsSet` errors, fall back: "I can't save changes to your store right now — your profile is unchanged. Try again next time." Do not silently retry.
- **Rollback.** Run this skill again with the previous values. The run log gives the merchant the data they need.

## Edge cases

- **Profile partially missing.** If `onboarding_state` is set but, say, `rituals` is null, treat null as "not yet set" and let the merchant fill it in this flow. Don't error.
- **Forget after declined.** If the merchant declined onboarding earlier, "forget my profile" still works — it just deletes the lone `onboarding_state` decline marker. After delete, next ask will trigger first-run again.
- **Switch-shop mid-flow.** Abort and tell the merchant to re-run after switch.
- **Schema drift.** If a future profile schema adds a field, accept the new field on edit; don't drop unknown fields read in step 1 — preserve them on write.

## Examples

- [`examples/basic.md`](examples/basic.md) — change operator role and write_defaults.
