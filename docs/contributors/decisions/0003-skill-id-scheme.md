# ADR 0003 — Skill ID scheme

**Status:** Accepted

## Decision

`<category>.<kebab-name>` — e.g. `catalog.product-status-audit`, `primitives.shop-context`.

Folder path mirrors the kebab portion: `skills/<category>/<kebab-name>/`.

## Why

- **Globally unique.** Category prefix prevents two skills colliding on the same kebab name.
- **Path-derivable.** Given an ID, you know exactly where the skill lives.
- **Sortable.** A directory listing groups by category naturally.
- **Speakable.** "Catalog dot product status audit" works in conversation.
- **Stable across renames.** If a skill graduates from `draft` to `stable`, its ID does not change. If a skill changes category, its ID changes — that's a breaking move and gets a new ADR.

## Considered and rejected

- **Pure kebab IDs (`product-status-audit`).** Too easy to collide. We will have multiple "audit" skills.
- **Verb-noun IDs (`audit.products`, `restock.inventory`).** Verbs and nouns get tangled — `audit.products` vs `products.audit` is arbitrary.
- **Hierarchical IDs (`catalog.products.status-audit`).** Nesting beyond one level adds friction. Stay flat.
- **UUID IDs.** Stable but unreadable; humans must edit these files.

## Reserved prefixes

- `primitives.*` — internal, not invoked directly by merchants.
- `meta.*` — documentation-style skills (e.g., `meta.wiring`).

## When to revisit

If two skills in different categories genuinely need the same kebab name and merchants get confused, revisit. Until then, this scheme is fine.
