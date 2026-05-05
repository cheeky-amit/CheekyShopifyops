# Contributing

Thanks for considering a contribution. This repo is a library of Claude Skills for the Shopify Admin MCP. The bar for inclusion is **merchant usefulness** plus **safety**.

## Before you write a skill

1. **Check the inventory.** Is there already a skill that does this? See `skills/` and [`docs/decisions/0005-skill-inventory.md`](docs/decisions/0005-skill-inventory.md). If your idea overlaps, propose composing existing primitives instead.
2. **Confirm MCP support.** The skill must work with the connected Shopify MCP only. If it needs a different MCP or external API, it doesn't belong here.
3. **Check the blocked list.** Refunds, gift card writes, staff management, theme publishing, and live theme writes are blocked at the MCP level. Don't try to route around them.

## Authoring a skill

1. Pick a category (`catalog`, `inventory`, `orders`, `customers`, `promotions`, `analytics`, `retention`, `storefront`, `reporting`, or propose a new one in your PR).
2. Copy an existing skill folder as a starting point.
3. Choose an ID: `<category>.<kebab-name>` — e.g., `inventory.cycle-count`. Folder path mirrors the ID's kebab portion.
4. Fill in `SKILL.md` per the frontmatter schema in [`docs/conventions.md`](docs/conventions.md).
5. Write `README.md` for humans: what it does, when to use it, what to expect.
6. Add at least one example in `examples/` with **generic placeholders only** (`Product A`, `customer@example.com`, `ORDER-1001`, `$XX.XX`).
7. If your skill writes, follow the safety contract in [`docs/safety.md`](docs/safety.md): preview → confirm → execute, compare-and-swap where supported, before/after log, rollback documented.

## Verifying

Run your skill against a real Shopify store **you control**. Use read-only paths fully. Use write paths via dry-run / preview only — never let your repo PR include data from a write that hit a live store.

If real data appears in a tool response during verification, **redact it before committing**. Search-and-replace `git diff` for store names, customer emails, real SKUs, real prices.

## Pull request checklist

- [ ] Skill ID follows `<category>.<kebab-name>` and matches folder path.
- [ ] Frontmatter complete per `docs/conventions.md`.
- [ ] `README.md` and at least one example included.
- [ ] All examples use generic placeholder data.
- [ ] Read paths verified against a real store.
- [ ] Write paths verified via dry-run only.
- [ ] No real store data in any committed file (titles, SKUs, emails, order numbers, GIDs, domains, prices).
- [ ] Safety section present in any write skill (preview, confirm, rollback).
- [ ] No skill duplicates an existing one.
- [ ] Commit messages contain no real data.

## Voice

Skills are for **non-technical merchants**. Don't write API reference. Write what a smart shop owner would understand:

- "Find products that are listed for sale but have no stock."
- Not: "Run `search_products` with `status:active AND inventory_total:0`."

The merchant doesn't see the tool calls. They see the result. Optimize the result for them.

## Filing an issue

If the MCP exposes a new capability, or a tool's behavior changes, file an issue with:

- The tool name
- A minimal reproducing call (with placeholder inputs)
- Expected vs actual behavior

If your idea isn't supported by the MCP, file it anyway — we keep a "wishlist" of things the MCP would need to expose for future skills.
