# shop-context

**Internal building block.** Not invoked directly by merchants. Used by other skills.

Loads the shop profile (currency, timezone, country, plan, name) and the merchant profile (operator, store stage, voice + write defaults, rituals, onboarding state) saved under `merchant_ai.*` metafields. Returns a single context object every other skill reads at step 0.

## Used by

Almost every skill. Recommended as the first step of any read or write workflow — domain skills use the shop profile for formatting and capability checks, and use the merchant profile to scale defaults and tone.

## What it does

- Reads `get-shop-info` for the shop profile.
- Reads four metafields under namespace `merchant_ai` for the merchant profile (operator, store, rituals, onboarding state).
- Returns a unified context object plus two flags: `needs_onboarding` (true → caller hands off to `onboarding.first-run`) and `onboarding_skipped` (true → caller uses defaults, no auto-trigger).
- Never returns the merchant's full email — only the email domain.

## See also

- [SKILL.md](SKILL.md) — full agent-facing instructions and return shape.
- [`onboarding.first-run`](../../onboarding/first-run/) — writes the `merchant_ai.*` metafields this primitive reads.
- [`onboarding.refresh-profile`](../../onboarding/refresh-profile/) — updates them.
