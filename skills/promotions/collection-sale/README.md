# Plan a collection sale

A friendlier wrapper around `discount-create` scoped to a specific collection, with a sanity-check on the revenue at stake.

> "Run a 20% off sale on the Spring 2026 collection, Mar 1 to Mar 15."
> "Discount the Spring collection."

## What it adds vs `discount-create`

- Collection resolution from name (not just GID).
- Recent-sales context for the collection ("this collection sold $X last 30 days") — so you know what you're discounting.
- Auto-suggested discount code if you don't pick one.
- Same rollback note (set `endsAt` to now via GraphQL).
