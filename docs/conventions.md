# Conventions

## Skill ID

Format: `<category>.<kebab-name>`

Examples:
- `catalog.product-status-audit`
- `inventory.cycle-count`
- `primitives.shop-context`
- `meta.wiring`

Rules:
- Globally unique within the repo.
- Folder path mirrors the kebab portion: `skills/<category>/<kebab-name>/`.
- Lowercase, hyphenated. No camelCase, no underscores in the kebab portion.

## Folder layout per skill

```
skills/<category>/<kebab-name>/
├── SKILL.md         # Required. Frontmatter + workflow.
├── README.md        # Required. Merchant-facing explanation.
└── examples/        # Required. ≥1 example.
    └── basic.md
```

Optional:
- `examples/edge-cases.md` — tricky situations
- `examples/with-graphql.md` — when a skill falls back to GraphQL for some path

## SKILL.md frontmatter

```yaml
---
id: catalog.product-status-audit
name: product-status-audit
title: Audit and fix product statuses
description: Find products in the wrong status (live without stock, draft with sales, archived but linked) and propose fixes.
audience: merchant         # merchant | contributor (primitives use 'contributor')
roles: [merchandiser, founder]
category: catalog
risk: write                # read | write | bulk-write
mcp_tools:                 # Direct tools used (verify these exist)
  - search_products
  - get-product
  - bulk-update-product-status
graphql:
  query: false
  mutation: false
triggers:                  # Phrases that should activate this skill
  - "audit my products"
  - "find products in the wrong status"
  - "which products are broken"
inputs:
  - name: dry_run
    type: boolean
    default: true
    description: When true, only preview changes; never call bulk-update-product-status.
  - name: cohort
    type: string
    default: "all"
    description: "Limit the audit (e.g. 'collection:summer-sale', 'vendor:Acme'). Free-text Shopify search syntax."
outputs:
  summary: |
    Bullet list of products with current → proposed status and a one-line rationale per product.
safety:
  blocks: []                                    # Operations this skill explicitly avoids
  requires_confirm: true                        # Will prompt before any write
  rollback: |
    bulk-update-product-status reverses individual product changes; the skill writes a before/after
    log so a merchant can ask "undo the last status sweep" and get the inverse change set.
status: stable             # draft | stable | deprecated
version: 1.0.0
---
```

### Field details

| Field | Required | Notes |
|---|---|---|
| `id` | yes | Globally unique. Format: `<category>.<kebab-name>` |
| `name` | yes | Kebab portion of the ID. |
| `title` | yes | Sentence-case, for humans. No "Skill:" prefix. |
| `description` | yes | One sentence. What it does, in plain language. ≤140 chars. |
| `audience` | yes | `merchant` for skills merchants invoke; `contributor` for primitives. |
| `roles` | no | Hint for which kind of merchant cares. Used in docs/index, not in routing. |
| `category` | yes | Top-level folder. |
| `risk` | yes | `read` (no writes), `write` (single-record writes), `bulk-write` (many records). |
| `mcp_tools` | yes | List the direct Shopify MCP tools the skill uses. Reviewers verify each one actually exists. |
| `graphql` | yes | Object: `query: bool`, `mutation: bool`. If either is true, the skill must follow the GraphQL workflow. |
| `triggers` | yes | ≥3 trigger phrases a merchant might say. Plain language, not jargon. |
| `inputs` | no | Named parameters. Document defaults explicitly. |
| `outputs` | yes | What the merchant sees. Brief. |
| `safety` | yes if `risk` ≠ `read` | `blocks`, `requires_confirm`, `rollback`. |
| `status` | yes | `draft` excluded from default surface; `stable` is the default for shipped skills. |
| `version` | yes | SemVer. Bump major on breaking output shape changes. |

## SKILL.md body structure

Below the frontmatter, every SKILL.md follows this order:

```markdown
## Purpose
1-2 sentences. What problem does this solve for a merchant?

## When to use
Specific triggers and example phrasings.

## When NOT to use
What this is not for. Point to the right alternative skill.

## Workflow
Numbered steps. Internal — describes what Claude does, can name tools.

## Output format
What the merchant sees. Show a generic example.

## Safety
Required for write skills. Preview/confirm/rollback specifics.

## Edge cases
What to do when:
- The store has 0 results
- Pagination cap is hit
- A required tool is unavailable
- Real data shape differs from expected

## Examples
Link to examples/. ≥1 with placeholder data only.
```

## Naming

- **Folder names**: `kebab-case`, matching the skill ID's kebab portion.
- **Skill files**: `SKILL.md` (uppercase), `README.md`, `examples/*.md` (`kebab-case.md`).
- **Trigger phrases**: lowercase, plain English. Imperative or question.
- **Categories**: single-word, lowercase, plural noun (`catalogs`? — no, **`catalog`**, treat as a domain not a count).
- **Placeholder data** (in examples and copy):

| Type | Placeholder |
|---|---|
| Product title | `Product A` (or `Product B`, `Product C` …) |
| Customer name | `Customer A` |
| Customer email | `customer@example.com` |
| Order name | `ORDER-1001` |
| SKU | `SKU-A` |
| Vendor | `Vendor A` |
| Tag | `tag-a` |
| Price | `$XX.XX` |
| GID | `gid://shopify/Product/000`, `gid://shopify/Order/000`, etc. |
| Domain | `example-shop.myshopify.com` |
| Date | `2026-01-15` (use realistic but obviously placeholder dates) |

Never use brand names, even fake-sounding ones, in placeholders. Never use real currency amounts that look intentional.

## Versioning

- New skill: `1.0.0`.
- Output shape change (a merchant who automates around this skill would notice): bump major.
- New optional input or expanded edge case handling: bump minor.
- Wording / docs / non-behavior changes: bump patch.

## Status lifecycle

`draft` → `stable` → `deprecated`

- `draft`: in progress; surfaced only when explicitly opted in.
- `stable`: shipped, recommended.
- `deprecated`: still works but a successor skill exists. SKILL.md must point to the successor.
