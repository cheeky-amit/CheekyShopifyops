# ADR 0004 — Naming conventions

**Status:** Accepted

## Decision

| Layer | Convention | Example |
|---|---|---|
| Skill ID | `<category>.<kebab-name>` | `catalog.product-status-audit` |
| Folder | `kebab-case` matching ID's kebab portion | `skills/catalog/product-status-audit/` |
| `SKILL.md` | uppercase | `SKILL.md` |
| `README.md` | uppercase | `README.md` |
| Examples | `kebab-case.md` | `examples/basic.md`, `examples/edge-cases.md` |
| Trigger phrases | lowercase, plain English imperatives or questions | `"audit my products"`, `"which products are broken"` |
| Categories | single-word, lowercase, domain-shaped | `catalog`, `inventory`, not `catalogs`, `Inventory` |
| Placeholder data | letter-suffixed generics | `Product A`, `Customer A`, `SKU-A`, `ORDER-1001`, `gid://shopify/Product/000` |

## Why

- **Uppercase `SKILL.md` / `README.md`** — instantly recognizable in a directory listing among the kebab-case examples.
- **Categories as singular** — they're domains, not counts. "catalog" reads as "the catalog domain"; "catalogs" reads as "multiple catalog records."
- **Trigger phrases lowercase** — they match how merchants type. Case doesn't matter for activation but consistency does.
- **Placeholder letter-suffixing (`Product A`, `Product B`)** prevents accidental collisions with real product names. No real merchant has a product literally named "Product A."
- **Date placeholders (`2026-01-15`)** — use ISO format, obvious recent year, mid-month, mid-day. Avoids real-looking dates that might match an actual launch.

## Considered and rejected

- `skill.md` (lowercase). Looks indistinguishable from random markdown.
- `index.md` for the skill body. "SKILL.md" is more explicit; this isn't a website.
- "Lorem ipsum" placeholder data. Reads as filler; merchants might think the skill is incomplete.
- Real-sounding fake brands ("Acme Coffee Co"). Too easy to accidentally write a real brand name. Generic letter suffix is safer.
