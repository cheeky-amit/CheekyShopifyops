# Onboarding skills

First-run setup, profile updates, and a guided tour. These run before (or alongside) the rest of the bundle and shape how the other skills talk to you.

- [`first-run/`](first-run/) — one-time setup: learns who's running the store and how you want Claude to behave, then saves a small profile to your store. Auto-runs the first time you ask Claude to do anything; you can also type "skip" and we'll use safe defaults.
- [`refresh-profile/`](refresh-profile/) — update the profile later. Use when a new operator takes over, the store changes stage, or you want different confirmation prompts. Also handles "forget my profile."
- [`tour/`](tour/) — read-only walkthrough of what Claude can do on your store. No questions, no writes. Run any time you want a quick reminder of what's available.
