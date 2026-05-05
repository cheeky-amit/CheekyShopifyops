---
id: storefront.new-store-preview
name: new-store-preview
title: Generate a new-store preview
description: Wrap the storefront preview generator with merchant-friendly intake — clarify the three required descriptors before calling the generator.
audience: merchant
roles: [founder]
category: storefront
risk: read
mcp_tools:
  - get-new-store-previews
  - get-storefront-generation
graphql:
  query: false
  mutation: false
triggers:
  - "create a new store"
  - "generate storefront previews"
  - "design a new shop"
  - "show me what my store could look like"
  - "preview a store for [idea]"
inputs:
  - name: product_or_service
    type: string
    description: What the store would sell. Required.
  - name: target_audience
    type: string
    description: Who the customers are. Required.
  - name: brand_style
    type: string
    description: Visual style / mood / palette. Required.
outputs:
  summary: |
    Up to 3 storefront previews. Generation takes ~3 minutes.
status: stable
version: 1.0.0
---

## Purpose

`get-new-store-previews` requires three short descriptive fields. The merchant rarely volunteers all three on first ask. This skill is the guided intake.

**Important:** Previews can only be claimed as **brand-new stores**. They cannot be applied to an existing storefront. The skill makes this explicit.

## When to use

- Merchant wants to start a new brand and explore design directions.
- Merchant is brainstorming visual variants before committing.

## When NOT to use

- Merchant wants to redesign their existing storefront. The previews can't be applied to an existing store. Direct them to Shopify themes or a designer.

## Workflow

1. **Confirm intent.** "These previews can only be claimed as brand-new stores — they can't be applied to an existing one. Are you starting a new brand?" If no, redirect to design tooling.
2. **Intake.** If any of `product_or_service`, `target_audience`, `brand_style` is missing or vague, ask. Don't guess. Don't compress past what the merchant said.
3. **Compress to keyword phrases** (≤78 chars each). Drop filler words; keep nouns and concrete descriptors. Don't add synonyms or invented attributes.
4. **Set `userUnderstandsNewStoreOnly: true`** since the merchant has explicitly confirmed.
5. **Call `get-new-store-previews`.**
6. **Tell the merchant** the previews take ~3 minutes; offer to do something else in the meantime.

## Output format

The host renders the preview widget. The skill's text output is a short framing:

```
Generating 3 storefront previews for:
  Product:    stoneware mugs, handmade, speckled glaze
  Audience:   urban 25-35, supports small artists
  Style:      earthy, warm, modern handmade, not rustic

This usually takes ~3 minutes. The previews will pop up here when ready.
Each one comes with a signup link — clicking it creates a brand-new
Shopify store with that theme and matching products pre-installed.

You can browse all three before committing to one.
```

## Edge cases

- **Existing store connected.** Confirm intent. The merchant is on a connected store — that doesn't mean they can't *also* start a new one, but the skill flags the ambiguity.
- **Vague descriptors.** Push back. "earthy" alone isn't enough. "earthy, warm, no neon" is.
- **Long descriptor.** Compress to ≤78 chars by dropping filler words.

## Examples

- [`examples/basic.md`](examples/basic.md)
