# Clean up product tags

Find tag mess — same tag spelled different ways ("Sale" vs "sale"), near-duplicates ("Tshirt" vs "T-shirt"), single-use oddballs — and consolidate them across your products.

## What you can say

> "Audit my tags."
> "Tag cleanup."
> "Fix tag typos."

## What you'll see back

You'll get clusters: case-only duplicates ("Sale", "sale", "SALE"), near-duplicates ("Tshirt", "T-shirt", "tshirts"), and single-use tags surfaced for review only. Each cluster proposes a single "winner" tag and shows how many products will change. You confirm the whole proposal before anything is written.

## When it will ask before doing anything

You always see the full proposal first — every cluster, every count, every product affected. Nothing happens until you type "yes." If a tag in a cluster is part of a smart collection's auto-rule (so renaming it would silently break that collection), the skill skips that cluster and tells you which collection to fix first.

## What it won't do

- Won't auto-fix single-use typos — too many false positives. Single-use tags are surfaced for your review only.
- Won't change collection rules. If a tag is part of a smart-collection rule, the skill flags it so you can update the rule in your Shopify admin first.
- Won't change anything else on the products — only their tag list.

## Want to see under the hood

It builds a histogram of every tag in your store, clusters them by case-equivalence and near-similarity, and updates each affected product's tag list in one pass after you confirm. The run log lists every product's previous tag list, so undo is a single ask.

## Related

- [`catalog.missing-content`](../missing-content/) — products with no tags at all.
- [`catalog.collection-coverage`](../collection-coverage/) — collections affected by tag changes.
