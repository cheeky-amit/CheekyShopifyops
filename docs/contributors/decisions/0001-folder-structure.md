# ADR 0001 — Folder structure

**Status:** Accepted

## Decision

Two-level skill hierarchy:

```
skills/
├── _system/<kebab-name>/         # Internal building blocks
└── <category>/<kebab-name>/      # Domain skills, organized by role intent
```

Categories are flat: `catalog`, `inventory`, `orders`, `customers`, `promotions`, `analytics`, `retention`, `storefront`, `reporting`, `meta`.

## Why

**Considered:**
1. Flat (`skills/<kebab-name>/`) — fast discovery, breaks down past ~20 skills.
2. By Shopify resource (`skills/products/`, `skills/orders/`, `skills/customers/`) — mirrors the API but doesn't match how merchants think. A "weekly brief" spans four resources.
3. By role (`skills/founder/`, `skills/cfo/`, `skills/cmo/`) — matches mental model but every skill ends up duplicated across roles.
4. **(Chosen)** By **role-shaped intent**, with primitives separated.

Role-intent categories cluster skills the way merchants categorize their work ("inventory things", "promotion things"). Primitives sit apart so they're not confused with merchant-invocable skills.

The `meta/wiring` doc-skill lives in `meta/` because it's neither a primitive nor a domain skill — it's a contributor-facing meta-document about composing this library with other tooling.

## What this rules out

- Per-role folders (`founder/`, `cfo/`, `cmo/`). Roles live in frontmatter `roles: [...]`, not folder paths.
- Per-tool folders (`search-products/`, `bulk-update/`). Tools are an implementation detail.
- Nested categories (`inventory/snapshots/by-location/`). Stay flat at category level.

## When to revisit

If a category exceeds ~10 skills and an obvious sub-grouping emerges, propose a sub-category in a new ADR. Don't pre-emptively nest.
