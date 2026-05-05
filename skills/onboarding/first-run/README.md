# first-run

A one-time setup so Claude knows who's running your store and how you want it to work. After this, every other skill behaves a little better — uses your name, scales defaults to your store's stage, and confirms changes the way you want.

It takes about a minute. You can also type **"skip"** at any time and we'll use safe defaults.

## When this runs

- **Automatically** the first time you ask Claude to do *anything* on a store that doesn't have a saved profile yet.
- **On request** when you say "set me up", "onboard me", "first-time setup", "I'm new", "getting started", or "help me get started".

## What you'll see

1. A short read-back of what we figured out from your store on our own — currency, plan, catalog size, recent volume, repeat-customer rate, locations.
2. Three quick questions about you and the store, plus one yes/no question about a weekly Monday brief.
3. A profile preview showing exactly what we'll save.
4. **Two confirmations.** First we ask "save this?" — then Shopify itself asks one more time before the save goes through. Two prompts is normal; it's the host (Shopify) double-checking.
5. A one-line "Saved. You're all set." and a prompt for what you want to work on.

## What we save

We save a small profile to your store — four little pieces of structured data (Shopify metafields are small bits of structured data your store can hold). Specifically:

- **Who you are** — your name and role, the voice you prefer, and how strict you want change-confirmations to be.
- **What stage your store is at** — just-launched / under-10k-mo / 10k-100k / 100k-1m / 1m+, plus a short model line and discipline signals.
- **Rituals** — whether you want a weekly brief, and which day.
- **Onboarding state** — when you completed it, so we don't ask again.

Your profile is saved on your store. It stays in your store; it's not sent anywhere else. (One small caveat: other apps you install on the same store with metafield-read permission could read it.)

## What we don't save

- Your email address.
- Anything about specific products, orders, or customers.
- Anything outside the small profile above.

## What if you skip?

Type "skip" or "use defaults" at any time. We save a "declined" marker on your store so we don't pester you for 30 days, and we use safe defaults: conversational tone, always preview, always confirm. You can run "set me up" later any time you change your mind.

## What if the save fails?

Sometimes the host blocks the write, or the network blips. If that happens we'll tell you: "I can't save your profile to your store right now — I'll remember it for this conversation only. Try again next time." Your session still works; the next session just starts fresh.

## Changing your mind later

- Want to update your role, your stage, or your preferences? Say "update my profile."
- Want it gone entirely? Say "forget my profile" — we delete the saved profile from your store.

## Examples

- [`examples/basic.md`](examples/basic.md) — full run, save profile.
- [`examples/skip.md`](examples/skip.md) — typing "skip" and using defaults.
