# Working contract — shopify-skills

Read this before editing or adding skills.

## Mission

Build a community library of Claude Skills for the Shopify Admin MCP. Two audiences: **merchants** (use seed skills as-is) and **contributors** (add skills using shared conventions).

## Hard rules

1. **Strict MCP scope.** Skills MUST only call the connected Shopify MCP. No external HTTP, no other MCPs. Exception: `meta/wiring` is documentation-only.
2. **Tool honesty.** Only reference tools that exist. If a workflow needs something the MCP doesn't expose, flag it and propose an alternative — never invent.
3. **No real store data anywhere.** Names, emails, SKUs, order numbers, prices, customer info, product titles, vendor names, domains — none of it appears in this repo. Use generic placeholders (`Product A`, `customer@example.com`, `ORDER-1001`, `$XX.XX`, `gid://shopify/Product/000`).
4. **Preview → confirm → execute** for every write skill. No exceptions.
5. **Compare-and-swap** wherever the MCP supports it (e.g., `set-inventory` `compareQuantity`).
6. **Document rollback** for every write skill, even if rollback is "manually re-set the previous value."
7. **Blocked ops stay blocked.** Refunds, gift card writes, staff management, theme publishing, and writes to live themes are out-of-scope. Document the alternative (Shopify admin UI) instead of routing around the block.

## GraphQL workflow

Whenever a skill uses `graphql_query` or `graphql_mutation`:

```
graphql_schema  →  search_docs_chunks (if needed)  →  validate_graphql_codeblocks  →  execute
```

Never call `graphql_mutation` without validation. Never guess field names.

## Skill format

Every skill folder contains:

- `SKILL.md` — frontmatter + workflow. Loaded by Claude when the skill activates.
- `README.md` — human-facing explanation. What the skill does, when to use it, examples.
- `examples/` — at least one example with **generic placeholder data only**.

Frontmatter schema in [docs/conventions.md](docs/conventions.md).

## Audience voice

Skills are for **merchants**, not developers. Voice rules:

- Plain language. Define jargon on first use ("GID — Shopify's internal product ID").
- Lead with the answer. Show the summary first, details after.
- No raw JSON dumps in skill output. Summarize.
- Never quote MCP tool names at merchants ("call `search_products`"). Say what it does ("look up your products").
- When asking for confirmation, show what will change in human terms ("12 products → ARCHIVED, 3 products → ACTIVE").

## Verification standard

Before committing a skill:

- **Read paths:** verify against the connected MCP. Confirm shape, pagination behavior, edge cases.
- **Write paths:** verify via dry-run / preview only. **Never** execute writes against a live store from this repo.
- **Redact:** if a tool response surfaces real data during verification, it does not get written to disk in any form.

## Decisions

Structural decisions are documented as ADRs in `docs/decisions/`. Add a new ADR when changing folder layout, frontmatter schema, ID scheme, or skill inventory.

## Don't

- Don't add a skill that duplicates an existing one. Compose primitives instead.
- Don't add a "framework" skill that does nothing on its own — every skill must answer a merchant question or do a merchant action.
- Don't add `.claude/`, `.cursor/`, or other client-specific tooling to the repo. This is a public skill library; contributors use whatever client they want.
- Don't include real screenshots, real CSV dumps, real anonymized-but-recognizable customer data.
