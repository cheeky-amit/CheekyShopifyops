# CheekyShopifyops

Run your Shopify store through Claude — in plain language. Drop this in, connect your store, and ask things like:

> "Show me products that are live but out of stock." → audit, with a fix plan.
> "What sold last week?" → trend with comparison to the week before.
> "I just counted 18 of SKU-A at the warehouse." → preview, confirm, safely adjust.
> "Build me a win-back list of lapsed VIPs." → cohort with profile, ready to use.
> "Weekly brief." → Monday-morning digest in one read.

35 ready-made skills, written for shop owners — not developers. Every action that changes your store shows you exactly what would change before doing it, and logs how to undo it.

## What you get

- **Catalog** — find products in the wrong status, missing content, duplicates, orphan products, tag taxonomy cleanup, bulk archive sweeps.
- **Inventory** — stock snapshots, low-stock alerts ranked by velocity, cycle counts (with safe compare-and-swap), shipment intake.
- **Orders** — today's pulse, stuck-fulfillment watchlist, plain-language order search.
- **Customers** — cohort snapshots, top spenders, marketing-consent audit.
- **Promotions** — create discount codes, plan a collection sale, audit what's running.
- **Analytics** — sales pulse over any range, top/bottom product performers, referrer mix, conversion funnel, new-vs-returning retention.
- **Retention** — win-back lists, first-order anniversary cohorts.
- **Storefront** — shop snapshot, new-store preview generator.
- **Reporting** — one composite weekly merchant brief.

Full list: [`skills/`](skills/). Each skill has a `README.md` written in plain language and a `SKILL.md` for the agent.

## Install

You need:
- A Shopify store you own.
- An MCP-compatible Claude client (Claude Desktop, Claude Code, Cursor, or other).
- The **Shopify Admin MCP** connected in your client.

### 1. Connect the Shopify Admin MCP

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
| Other MCP clients | Most accept a skills/plugins path — check that client's docs |

Restart your client. The skills should now activate when you ask things like "audit my products" or "show stuck orders."

### 3. Try it

Start with a read-only skill so you can see what the bundle does without risk:

> "Shop snapshot."
> "Pulse." (today's orders)
> "Top products last 30 days."
> "What's running low?"

Then try a write skill — every write previews first and waits for your confirmation:

> "I counted 18 of SKU-A at the Warehouse." (cycle count)
> "Audit my products." (status drift; dry-run by default)

## How safe is this?

Every action that changes your store follows the same five-step contract:

1. **Read** current state.
2. **Preview** the change as a clear before → after diff.
3. **Confirm** with you. Bulk changes require explicit confirmation including the count.
4. **Compare-and-swap** where Shopify supports it — so a parallel sale can't be silently overwritten by a stale write (this is critical for inventory).
5. **Log** before/after with rollback instructions.

Some Shopify operations are **blocked at the MCP level** and these skills do not attempt them: refunds, gift card writes, staff management, theme publishing, and writes to your live theme. If you ask for one of those, the skill tells you and points you to the Shopify admin path. Full contract in [`docs/contributors/safety-contract.md`](docs/contributors/safety-contract.md).

## What this can't do

By design, this bundle is **strict Shopify MCP only** — no email sending, no SMS, no supplier/3PL connections, no accounting sync, no ad platforms. Anything off-Shopify needs another MCP.

If you want to combine these with other tools (e.g., send a win-back email to the cohort this bundle builds for you), see [`skills/meta/wiring/`](skills/meta/wiring/) for how to compose with other MCPs in your own project.

## Repo layout

```
CheekyShopifyops/
├── skills/
│   ├── _system/        # Building blocks the other skills use (not invoked directly)
│   ├── catalog/        # Product audits, content, dedup, tag cleanup
│   ├── inventory/      # Snapshot, low-stock, cycle count, receive
│   ├── orders/         # Pulse, fulfillment watchlist, search
│   ├── customers/      # Cohort, top spenders, subscriber audit
│   ├── promotions/     # Discount create, audit, collection sale
│   ├── analytics/      # Pulse, product perf, referrer, funnel, retention
│   ├── retention/      # Win-back, anniversary
│   ├── storefront/     # Shop snapshot, new-store preview
│   ├── reporting/      # Weekly merchant brief
│   └── meta/wiring/    # Combining with other MCPs
├── docs/               # Architecture, conventions, safety, ADRs
└── examples/           # Cross-skill workflow examples
```

## Contributing

Pull requests welcome. Quick rules:

- Skills speak to **merchants**, not developers.
- Every write skill follows the safety contract — no exceptions.
- **No real store data** in any committed file (titles, SKUs, customer emails, order numbers, GIDs, domains, prices). Use the placeholder convention: `Product A`, `customer@example.com`, `ORDER-1001`, `$XX.XX`, `gid://shopify/Product/000`.

Full guide: [CONTRIBUTING.md](CONTRIBUTING.md). Conventions and frontmatter schema: [`docs/contributors/conventions.md`](docs/contributors/conventions.md).

## License

MIT. See [LICENSE](LICENSE).
