# Collection coverage check

See which products aren't in any collection (orphans) and which collections have no products in them (empties).

## What you can say

> "Audit my collections."
> "Orphan products."
> "Empty collections."

## What you'll see back

Two lists. First, **orphan products**: active products that aren't in any collection — these usually won't show up in your storefront's collection-based navigation. Second, **empty collections**: collections with no products in them — usually a smart-collection rule that no longer matches anything, leftover test data, or a launch that was never populated. Each row tells you why it's empty so you know whether to fix it or delete it.

## What it won't do

- Won't add or remove products from collections. The audit surfaces what's wrong; you choose how to fix.
- Won't delete empty collections — that's a deliberate action you do in your Shopify admin.

## Want to see under the hood

It walks your active products and your collections, cross-references them, and reports the two gaps. No writes.

## Related

- [`catalog.tag-cleanup`](../tag-cleanup/) — fix tag mess that breaks smart collections.
- [`catalog.duplicate-detector`](../duplicate-detector/) — likely duplicate listings.
