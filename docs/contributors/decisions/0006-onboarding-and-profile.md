# ADR 0006 — Onboarding and merchant profile storage

**Status:** Accepted (Task 2 of merchant-first redesign)

## Decision

1. **Storage.** The merchant profile lives in shop-level metafields under a single namespace `merchant_ai`, with four keys: `operator_profile`, `store_profile`, `rituals`, `onboarding_state`. Type `json` on all four. Owner type `Shop`.
2. **Trigger.** First-run onboarding auto-triggers when `_system.shop-context` returns `needs_onboarding: true`. The originating skill calls `onboarding.first-run` silently, completes it, then resumes its own work.
3. **Skip semantics.** A merchant can decline at any point during first-run. Decline writes only `onboarding_state` with `declined_at`. While `declined_at` is within 30 days, downstream skills proceed with defaults and do not auto-trigger onboarding again.
4. **Single operator per store, v1.** One `operator_profile` per shop. Multi-operator (different humans, same store, different preferences) is a v2 concern.
5. **Consumer.** `_system.shop-context` is the only reader; every other skill goes through it. No other skill reads `merchant_ai.*` directly.

## Rationale

### Why metafields and not a local file

Metafields are portable — every Shopify store keeps its own profile, and switching stores naturally rescopes. A local file in this skill repo would couple profile to the operator's machine / client install rather than to the store, and would lose state on `switch-shop`. Metafields are durable, queryable, and survive client switches.

### Why namespace `merchant_ai` and not e.g. `shop_skills`

- **Durable across renames.** This skill bundle may be renamed or relocated. `merchant_ai` reads as "this is the AI assistant's data," not "this is the shop-skills repo's data," so a future rename doesn't invalidate the namespace.
- **Signals intent clearly.** Apps inspecting the shop's metafields can see at a glance that these are AI-assistant-managed.
- **Won't collide.** The merchant's own metafield namespaces are usually domain-prefixed (e.g. `custom`, `app--<id>`); `merchant_ai` is unlikely to conflict.

### Why auto-trigger first-run and not require an explicit "set me up"

- Most merchants will skip explicit setup if it's optional. A frictionless auto-trigger captures more profiles, and the merchant can always type "skip" — that decline is itself a signal we record.
- Downstream skills behave noticeably better with even a partial profile; degrading gracefully when there's no profile is fine, but optimizing for the with-profile path matters.
- The 30-day decline window prevents the auto-trigger from feeling pushy.

### Why a single operator profile in v1

- v1 covers solopreneurs and small teams where one person sets preferences and others mostly accept them.
- Multi-operator extension would need: per-operator identity (we don't have one — Shopify auth doesn't tell us who's typing), per-operator preferences keyed off that identity, and conflict resolution. None of those are tractable in v1 without a dedicated identity primitive.
- v2 can extend `operator_profile` to a list keyed by some identity hint without breaking v1 readers (they'd take the first entry).

## Considered and rejected

| Alternative | Why rejected |
|---|---|
| **Local profile file** in the skill repo (e.g. `.shop-skills/profile.json`). | Couples to client install, not to store. Loses state on switch-shop. Pollutes a public skill repo with per-merchant data. Violates the "no `.claude/` or client tooling" rule in CLAUDE.md. |
| **No persistence — re-derive every session.** | Most useful fields (operator name/role, voice preference, write-defaults) cannot be derived from store data at all. Re-deriving the rest each session burns API calls and asks the merchant the same questions repeatedly. |
| **Per-conversation storage** (model memory only). | Loses on session restart. Defeats the purpose of "every skill knows you." |
| **Customer-scoped metafields** (use the merchant's customer record). | Most merchants don't have a customer record on their own store. Wrong owner type. |
| **App-installed metaobjects** instead of metafields. | More overhead (need a metaobject definition), no upside for a 4-field profile. Defer to v2 if the profile grows. |
| **Multiple namespaces** (one per concern, e.g. `mai_operator`, `mai_store`). | Four GraphQL alias reads in one query is identical regardless. Single namespace is tidier; matches how merchants think about it ("the AI thing's data"). |

## Open questions

- **Multi-operator extension shape.** Likely v2: change `operator_profile` from object to list of objects keyed by some identity hint, with a `default_operator` field. Backward compatible if v1 readers take the first entry.
- **Privacy / `private` metafield flag.** Whether to mark `merchant_ai.*` as `private` (hidden from storefront and API consumers other than this app) depends on the app permissions setup at install time. Default for v1: not private — the merchant owns the data and we don't surface it on storefront.
- **Profile history.** Should refresh-profile keep an N-deep history of previous profile values for rollback? v1 says no — the run log the skill emits is enough. v2 may add history if merchants ask.
- **Cross-store profile portability.** Should there be a "copy my profile from store X to store Y" flow? Probably yes in v2 for multi-store merchants. Out of scope for v1.

## See also

- [`docs/contributors/profile-schema.md`](../profile-schema.md) — full schema, validation, GraphQL ops.
- `skills/_system/shop-context/` — the primitive that reads `merchant_ai.*`.
- `skills/onboarding/first-run/` — primary writer (auto-triggered).
- `skills/onboarding/refresh-profile/` — editor + delete path.
- `skills/onboarding/tour/` — read-only walkthrough; reads onboarding state to pick its CTA.
