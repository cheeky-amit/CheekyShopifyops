# ADR 0002 — Frontmatter schema

**Status:** Accepted

## Decision

Every `SKILL.md` carries YAML frontmatter with required fields: `id`, `name`, `title`, `description`, `audience`, `category`, `risk`, `mcp_tools`, `graphql`, `triggers`, `outputs`, `status`, `version`. Plus `safety` for non-read skills, plus optional `roles`, `inputs`. See `docs/conventions.md` for the full schema and field definitions.

## Why this set of fields

**`mcp_tools` is the safety contract.** By forcing every skill to enumerate the direct MCP tools it uses, reviewers can grep the repo for skills that reference a non-existent tool. It also makes "tool honesty" auditable: if `mcp_tools` lists `bulk-update-product-status` but the skill body never uses it, that's a smell.

**`graphql: { query, mutation }` flags the validation workflow.** Any skill with `mutation: true` is a higher-review target — reviewers ensure `graphql_schema → validate_graphql_codeblocks → execute` is followed.

**`risk` drives behavior.** `read` skills don't need a `safety` block. `write` and `bulk-write` do. Tooling can later use this to gate confirmation prompts and rate-limit bulk skills.

**`triggers` are merchant phrases, not regex.** They're hints for the activating layer (host client) and a sanity check that the skill is named for what merchants ask. If you can't write 3 trigger phrases, the skill is too vague.

**`status` enables draft skills** without surfacing them by default.

## Considered and rejected

- Free-form description-only skills (no frontmatter). Easier to author; impossible to govern.
- A heavier schema with `permissions`, `cost`, `latency_class`. Premature — add when we have data.
- JSON instead of YAML. YAML wins on readability for humans authoring SKILL.md files.

## When to revisit

Once we have ~20 skills and a host client that consumes the frontmatter (e.g., an index page, a CLI lister), we may add fields. Bump the schema version in this ADR when changing required fields.
