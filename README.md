# Shopify Skills

A community-built library of Claude Skills for the **Shopify Admin MCP**. Plug it into Claude (Desktop, Code, or any MCP-aware client), connect your Shopify store, and run your shop in plain language.

> "Show me products that are live but out of stock." → audit + fix plan.
> "What sold last week?" → analytics summary.
> "Restock 30 units to SKU-A at the warehouse." → preview, confirm, write.

## Who this is for

**Merchants** — non-technical store owners. Skills speak in plain language, return useful summaries, and never mutate your store without showing you exactly what would change first.

**Contributors** — anyone who wants to add a skill. The conventions are documented; copy a primitive, follow the safety contract, send a PR.

## What's inside

```
shopify-skills/
├── skills/
│   ├── primitives/     # Internal building blocks (shop-context, graphql-helper, safe-write…)
│   ├── catalog/        # Product status, missing content, duplicates, tag cleanup
│   ├── inventory/      # Snapshot, low stock, cycle counts, receive shipment
│   ├── orders/         # Daily pulse, fulfillment watchlist, search
│   ├── customers/      # Cohorts, high-value, subscriber audit
│   ├── promotions/     # Discount create, audit, collection sale
│   ├── analytics/      # Daily pulse, product perf, referrer mix, conversion, retention
│   ├── retention/      # Win-back, anniversary
│   ├── storefront/     # New-store preview, shop snapshot
│   ├── reporting/      # Weekly merchant brief
│   └── meta/wiring/    # Adding other MCPs / dropping these into another project
├── docs/               # Architecture, conventions, safety, decisions
└── examples/           # Cross-skill workflow examples
```

## Install

This repo is a **skill bundle**. Each skill is a self-contained folder with a `SKILL.md` and a `README.md`. To use them with Claude Code:

```bash
git clone https://github.com/<org>/shopify-skills.git ~/projects/shopify-skills

# Symlink into your Claude Code skills directory (path varies by client)
ln -s ~/projects/shopify-skills/skills ~/.claude/skills/shopify-skills
```

Then connect the **Shopify MCP** to your client per [Shopify's docs](https://shopify.dev/docs/apps/build/mcp).

For detailed wiring (adding other MCPs alongside these skills, or dropping these into a project that already has other MCPs), see [`skills/meta/wiring`](skills/meta/wiring/SKILL.md).

## Safety contract

Every skill that mutates your store follows the same pattern:

1. **Read** current state.
2. **Preview** the change as a diff (current → proposed).
3. **Confirm** with you before any write.
4. **Compare-and-swap** where the MCP supports it (e.g., `set-inventory` `compareQuantity`).
5. **Log** before/after snapshots.
6. **Rollback** documented in every write skill.

Operations that are **blocked** by the Shopify MCP (refunds, gift card writes, staff management, theme publishing, live theme writes) are documented as out-of-scope. Skills do not attempt them.

See [`docs/safety.md`](docs/safety.md) for the full contract.

## Scope

**In scope:** anything the Shopify Admin MCP exposes — products, collections, inventory, orders (read), customers (read), discounts (create), analytics (ShopifyQL), and the Admin GraphQL surface for everything else (metafields, metaobjects, pages, blogs, markets, translations, publications…).

**Out of scope:** anything requiring a different MCP (email/SMS sending, third-party fulfillment, accounting integrations, etc.). One exception: the [`meta/wiring`](skills/meta/wiring/SKILL.md) skill explains how to combine this bundle with other MCPs in your own project.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Short version: copy an existing skill folder, follow the frontmatter schema in [`docs/conventions.md`](docs/conventions.md), verify against a real store using read-only paths, never include real store data in PRs.

## License

MIT. See [LICENSE](LICENSE).
