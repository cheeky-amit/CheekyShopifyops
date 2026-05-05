# Wiring — combine these skills with other MCPs

This skill bundle is **strict Shopify MCP only**. But many real workflows span multiple tools (email, ops, BI, accounting). The wiring skill is the single documented place that explains how to combine this bundle with other MCPs without breaking its safety contract.

It is documentation, not integration. It doesn't call other MCPs. It explains the patterns.

## Two scenarios it covers

1. **Adding an MCP alongside this bundle** — you started with `shopify-skills`, you want to add Klaviyo (or Slack, Notion, etc.).
2. **Dropping this bundle into a project that already has other MCPs** — you have a project, you want these Shopify skills to live alongside what you already have.

## Why this is documentation-only

Cross-bundle skills (a skill that calls Shopify + something else) belong in a *project-specific* skill bundle, not in `shopify-skills`. Keeping `shopify-skills` strict-MCP makes it portable — anyone can drop it into any project without inheriting opinions about which other tools you use.

Your project's cross-bundle skills can compose these as building blocks.
