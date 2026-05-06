#Shopifyops

Run your Shopify store through Claude — in plain language. Drop this in, connect your store, and ask things like:

> "Show me products that are live but out of stock." → audit, with a fix plan.
> "What sold last week?" → trend with comparison to the week before.
> "I just counted 18 of Product A at the warehouse." → preview, confirm, safely adjust.
> "Build me a win-back list of lapsed VIPs." → cohort with profile, ready to use.
> "Weekly brief." → Monday-morning digest in one read.

Ready-made skills, written for shop owners — not developers. Every action that changes your store shows you exactly what would change before doing it, and tells you how to undo it.

**First time here?** Once your store is connected, say "set me up" — the onboarding skill will spend about 60 seconds learning your store and how cautious you want it to be on changes. After that, just ask things in plain English.

## What you get

- **Catalog** — find products in the wrong status, missing content, duplicates, orphans, tag taxonomy cleanup, bulk archive sweeps.
- **Inventory** — stock snapshots, low-stock alerts ranked by velocity, cycle counts (with a safety check), shipment intake.
- **Orders** — today's pulse, stuck-fulfillment watchlist, plain-language order search.
- **Customers** — cohort snapshots, top spenders, marketing-consent audit.
- **Promotions** — create discount codes, plan a collection sale, audit what's running.
- **Analytics** — sales pulse over any range, top/bottom product performers, referrer mix, conversion funnel, new-vs-returning retention.
- **Retention** — win-back lists, first-order anniversary cohorts.
- **Storefront** — shop snapshot, new-store preview generator.
- **Reporting** — one weekly merchant brief that pulls the highlights from the rest.

Full list: [`skills/`](skills/). Each skill has a `README.md` written in plain language and a `SKILL.md` for the agent.

## Install

You need:
- A Shopify store you own.
- A Claude client that can connect to external tools — Claude Desktop, Claude Code, Cursor, or another MCP-compatible client (MCP is the connector standard Claude uses to talk to Shopify).
- The **Shopify Admin connector** turned on in your client.

### 1. Connect the Shopify Admin connector

Follow [Shopify's instructions](https://shopify.dev/docs/apps/build/mcp). In Claude Desktop and Claude Code, this is one config entry; once connected, you'll be prompted to authorize access to your store on the first call.

### 2. Add these skills to your client

Clone the repo:

```bash
git clone https://github.com/cheeky-amit/CheekyShopifyops.git
```

Then point your client at the `skills/` folder. Path varies by client:

| Client | How |
|---|---|
| Claude Code | `ln -s $(pwd)/CheekyShopifyops/skills ~/.claude/skills/shopify` (or copy the folder there) |
| Claude Desktop | Add the path to the skills directory in your config |
| Other clients | Most accept a skills/plugins path — check that client's docs |

The bundle includes a one-time onboarding folder (`skills/onboarding/`) that runs on your first connect, plus shared building blocks in `skills/_system/` that the other skills use under the hood. You don't need to do anything different — they activate as needed.

Restart your client. The skills should now activate when you ask things like "audit my products" or "show stuck orders."

### 3. Try it

The first time you connect, say:

> "Set me up."

That kicks off a 60-second onboarding. After that, start with a read-only ask so you can see what the bundle does without risk:

> "Shop snapshot."
> "Pulse." (today's orders)
> "Top products last 30 days."
> "What's running low?"

Then try a write skill — every write previews first and waits for your confirmation:

> "I counted 18 of Product A at the Warehouse." (cycle count)
> "Audit my products." (status drift; preview by default)

## What stays safe

Every action that changes your store first shows you exactly what would change. Bulk changes need a clear "yes — change these N." Inventory writes have a safety check so a sale that happened mid-count can't get silently overwritten. Some operations (refunds, gift cards, theme publishing, staff management) are blocked entirely and the skills point you at the right Shopify admin path instead.

Full plain-English contract: [`docs/how-this-stays-safe.md`](docs/how-this-stays-safe.md). Full list of what this bundle can't do: [`docs/what-this-cant-do.md`](docs/what-this-cant-do.md).

## Cookbook

Real day-in-the-life flows — short transcripts to read top-to-bottom. Each chains a few skills together to solve one recurring problem.

- [First-time setup](examples/first-run.md) — what happens the first time you connect a store.
- [Monday morning](examples/monday-morning.md) — the standard end-of-week pulse.
- [Catalog cleanup day](examples/catalog-cleanup-day.md) — quarterly housekeeping for your products.
- [Receiving a shipment](examples/post-shipment-intake.md) — PO arrived; here's the flow.
- [Launching a sale](examples/pre-sale-launch.md) — picking what to discount, scoping it safely.
- [Win-back cycle](examples/win-back-cycle.md) — building a re-engagement list.

## Repo layout

```
CheekyShopifyops/
├── CLAUDE.md            # Merchant operator's manual (start here)
├── README.md            # This file
├── skills/
│   ├── onboarding/      # First-run setup + tour + profile refresh
│   ├── _system/         # Shared building blocks the other skills use (not invoked directly)
│   ├── catalog/         # Product audits, content, dedup, tag cleanup
│   ├── inventory/       # Snapshot, low-stock, cycle count, receive
│   ├── orders/          # Pulse, fulfillment watchlist, search
│   ├── customers/       # Cohort, top spenders, subscriber audit
│   ├── promotions/      # Discount create, audit, collection sale
│   ├── analytics/       # Pulse, product perf, referrer, funnel, retention
│   ├── retention/       # Win-back, anniversary
│   ├── storefront/      # Shop snapshot, new-store preview
│   ├── reporting/       # Weekly merchant brief
│   └── meta/wiring/     # Combining with other tools (email, SMS, ads, etc.)
├── docs/
│   ├── how-this-stays-safe.md
│   ├── what-this-cant-do.md
│   └── contributors/    # Engineering docs (architecture, conventions, ADRs, safety contract)
└── examples/            # Cookbook — multi-skill day-in-the-life stories
```

## Contributing

Pull requests welcome. Quick rules:

- Skills speak to **merchants**, not developers.
- Every write skill follows the safety contract — no exceptions.
- **No real store data** in any committed file (titles, SKUs, customer emails, order numbers, GIDs, domains, prices). Use the placeholder convention: `Product A`, `customer@example.com`, `ORDER-1001`, `$XX.XX`, `gid://shopify/Product/000`.

Full guide: [CONTRIBUTING.md](CONTRIBUTING.md). Engineering details: [`docs/contributors/`](docs/contributors/).

## License

MIT. See [LICENSE](LICENSE).
