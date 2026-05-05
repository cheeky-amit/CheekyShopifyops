# Wiring — combining this bundle with other MCPs

This skill bundle is **strict Shopify MCP only.** No external HTTP, no other MCPs. But many real workflows span multiple tools — email, ops, BI, accounting — and this skill is the single documented place that explains how to combine `shopify-skills` with other MCPs without breaking its safety contract.

It is documentation, not integration. It does not call other MCPs. It explains the patterns.

## Two scenarios it covers

1. **Adding an MCP alongside this bundle.** You started with `shopify-skills` and want to add Klaviyo (or Slack, Notion, an analytics MCP, etc.) so a workflow can read from Shopify and act in the other tool.
2. **Dropping this bundle into a project that already has other MCPs.** You have a multi-MCP project and want these Shopify skills to live alongside what you already have, without conflicting frontmatter, conflicting names, or accidental cross-bundle calls.

## Why this is documentation-only

Cross-bundle skills (a skill that calls Shopify + something else) belong in a *project-specific* skill bundle, not in `shopify-skills`. Keeping `shopify-skills` strict-MCP makes it portable — anyone can drop it into any project without inheriting opinions about which other tools you use.

Your project's cross-bundle skills can compose these as building blocks: read from a Shopify skill, then act in the other MCP from your project's own skill.

## See also

- [SKILL.md](SKILL.md) — patterns and worked examples for both scenarios.
- [working-contract.md](../../../docs/contributors/working-contract.md) — the strict-MCP rule and other contributor invariants.
