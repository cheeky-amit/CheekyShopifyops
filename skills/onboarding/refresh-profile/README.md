# refresh-profile

The merchant-side update tool for the profile that `first-run` saved. Use this when:

- A new operator takes over.
- The store grows into a new stage.
- You want fewer (or more) confirmation prompts.
- You want the Monday brief on or off.
- You want to wipe the saved profile entirely ("forget my profile").

## When this runs

- On request: "update my profile", "I'm a new operator", "we just hired", "the store changed", "my role changed", "refresh my profile", "forget my profile".

## What you'll see

1. The profile we currently have on file.
2. A prompt asking which fields you want to change.
3. A profile preview showing your edits in `was → now` form.
4. **Two confirmations.** First we ask "save changes?" — then Shopify itself asks once more before the change goes through.
5. A short success line and a run log so you can roll back later if you need to.

## "Forget my profile"

If you say "forget my profile", we delete the saved profile from your store. We'll ask one explicit "are you sure?" prompt before doing it, and Shopify will ask once more. After delete, the next time you ask Claude for anything, the first-run flow triggers again — type "skip" if you want to stay on defaults.

## What we never touch

- Your products, orders, customers, inventory, or anything else outside the small profile we saved at first-run.

## What if the save fails?

Same fallback as first-run: "I can't save changes to your store right now — your profile is unchanged. Try again next time." Your data on the store is left exactly as it was.

## Examples

- [`examples/basic.md`](examples/basic.md) — change operator role and write-defaults.
