# Audit and consolidate product tags

Find tag taxonomy issues and propose consolidations. Cleans up case-duplicates ("Sale" vs "sale"), near-duplicates ("Tshirt" vs "T-shirt"), and surfaces single-use tags for review.

> "Tag cleanup."
> "Audit my tags."
> "Fix tag typos."

## What it does

1. Builds a tag histogram from your products.
2. Clusters tags by case-equality and near-similarity.
3. Surfaces single-use tags separately (review only, never auto-changed — many are intentional per-launch tags).
4. You confirm.
5. Updates each affected product to the consolidated tag list.

## Smart-collection awareness

If a tag in a cluster is used as a rule on an active smart collection, renaming it would break the collection. The skill warns and either skips the cluster or asks you to update the rule first.

## What this skill won't do

- Auto-fix single-use typos. Too many false positives (intentional rare tags). Fix manually.
- Update smart-collection rules. Out of scope; the skill flags affected collections so you can fix them in Shopify admin.
