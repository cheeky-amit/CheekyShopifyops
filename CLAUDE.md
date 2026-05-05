# Welcome — running your Shopify store with Claude

This is a bundle of ready-made skills for running your Shopify store through plain conversation. Connect your store, ask in normal English, and get answers, audits, and (when you say so) safe changes back.

## How to talk to it

- Talk normally. You don't memorize commands.
- Ask "what can you do?" any time and you'll get a tour.
- If you're not sure what to ask, try "shop snapshot" or "weekly brief."

## First time here?

Run "set me up" — or just ask anything; the first ask will trigger setup. Takes about 60 seconds. The skill learns who you are, what stage your store is at, and how cautious you want it to be on changes. Everything else gets tailored from there.

## What it can do for you

**Catalog** — audit your products and clean them up. Try "find live products with no stock," "show duplicates," "audit my tags."

**Inventory** — see what you have, what's running low, and reconcile counts. Try "what's running low?" "I counted 18 of Product A at the warehouse," "shipment came in for vendor X."

**Orders** — pulse on today and watch for stuck shipments. Try "pulse," "show stuck orders," "find ORDER-1001."

**Customers** — see who buys, who's loyal, who's on your list. Try "top 30 spenders," "subscriber audit," "lapsed VIPs."

**Promotions** — plan a sale and check what's already running. Try "create a 15% code SUMMER15," "plan a 20% sale on Collection X," "what discounts are live?"

**Analytics** — see how the store is trending. Try "sales last 30 days vs prior 30," "top and bottom products," "where's traffic coming from?"

**Retention** — find people worth bringing back. Try "build a win-back list," "who's near their first-order anniversary?"

**Storefront** — store-level checks. Try "shop snapshot," "show me theme previews from a description."

**Reporting** — one read at the start of the week. Try "weekly brief."

## What stays safe

- Nothing changes without showing you first.
- Bulk changes need a clear "yes — change these N" from you.
- Inventory writes use a safety check so a sale that just happened can't get silently overwritten.
- Some risky things are blocked entirely: refunds, gift cards, theme publishing, staff management. Those go through your Shopify admin.

Full safety story: [docs/how-this-stays-safe.md](docs/how-this-stays-safe.md).

## What it can't do

- Send email or SMS — that needs a different tool.
- Place supplier orders or sync to accounting — also other tools.
- Touch your live theme or add staff — blocked by Shopify.

Full list: [docs/what-this-cant-do.md](docs/what-this-cant-do.md). To combine these skills with email/SMS/etc., see [skills/meta/wiring](skills/meta/wiring/README.md).

## When it gets stuck

- Tell it more specifically (vendor, date range, customer name).
- Ask it to narrow the search.
- If you have multiple stores, say "switch shop" and pick one.

## Want to add a skill or change one?

See [CONTRIBUTING.md](CONTRIBUTING.md). The technical details live in [docs/contributors/](docs/contributors/).
