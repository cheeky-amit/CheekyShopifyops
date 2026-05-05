# Bulk-archive (or activate) by cohort

When you already know which products should change status — by cohort or collection — this skill applies the change to up to 50 products at once.

> "Archive all out-of-stock products from Vendor A."
> "Set the Spring 2025 collection to draft."
> "Bulk-archive everything tagged 'discontinued'."

## Why a hard 50 cap

`bulk-update-product-status` caps at 50 per call. This skill does NOT paginate past that. Bigger sweeps require multiple runs with narrower cohorts. The intent is to keep blast radius small — a sweep that changes 500 products with one "yes" is too easy to regret.

## Confirmation strictness

For bulk writes, the skill requires unambiguous confirmation: the merchant must echo the count and direction (e.g., "yes — archive these 28"). "ok" alone is not enough.

## Difference from `product-status-audit`

- `product-status-audit` finds drift across multiple directions (archive some, activate some, draft some) and proposes per-product fixes.
- `bulk-status-sweep` applies a single target status to a cohort the merchant already chose.

## Sister skills

- `catalog.product-status-audit` — multi-direction status fixes.
- `catalog.tag-cleanup` — for tag changes, not status.
