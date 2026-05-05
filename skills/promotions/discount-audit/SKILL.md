---
id: promotions.discount-audit
name: discount-audit
title: Active discount audit
description: List active and upcoming discount codes — type, percentage, scope, validity, redemption count — and flag stale or about-to-start ones.
audience: merchant
roles: [cmo, founder, cfo]
category: promotions
risk: read
mcp_tools:
  - get-shop-info
graphql:
  query: true
  mutation: false
triggers:
  - "active discounts"
  - "what discounts do I have running"
  - "audit my discount codes"
  - "discount audit"
  - "any expired codes"
inputs:
  - name: include_expired
    type: boolean
    default: false
outputs:
  summary: |
    Three sections: active now, upcoming, expired (if requested). Each row: code, type, scope,
    validity, redemptions.
status: stable
version: 1.0.0
---

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity.
1. There is no direct tool for listing discount nodes. Use `_system/graphql-helper` (kind: query) on `discountNodes`.
2. Query (validate first):
   ```
   query DiscountList($after: String) {
     discountNodes(first: 50, after: $after) {
       edges {
         node {
           id
           discount {
             __typename
             ... on DiscountCodeBasic {
               title
               codes(first: 1) { edges { node { code } } }
               status
               summary
               startsAt
               endsAt
               codeCount
               usageLimit
               asyncUsageCount
               customerSelection { __typename }
               customerGets { value { __typename ... on DiscountPercentage { percentage } } }
             }
             ... on DiscountAutomaticBasic { ... }
             ... on DiscountCodeBxgy { ... }
             ... on DiscountCodeFreeShipping { ... }
           }
         }
       }
       pageInfo { hasNextPage endCursor }
     }
   }
   ```
3. Bucket by status (`ACTIVE`, `SCHEDULED`, `EXPIRED`).
4. Render.

## Output format

```
example-shop · discount audit

→ ACTIVE NOW                                                    3
   SPRING20         20%   Collection: Spring 2026     ends 2026-03-15   42 redemptions
   WELCOME10        10%   All / new customers         no end            1,840 redemptions
   FREESHIP         FREE  All / orders ≥ $50          ends 2026-02-28   124 redemptions

→ UPCOMING                                                      1
   FLASH40          40%   All                         starts 2026-02-14, ends 2026-02-15   ⚠ steep

→ EXPIRED (last 30 days, included on request)                   2
   HOLIDAY15        15%   All                         expired 2025-12-31    320 redemptions
   BFRIDAY30        30%   Collection: Apparel         expired 2025-12-01    480 redemptions
```

## Edge cases

- **No discount nodes.** "No discounts found."
- **Discount types not yet covered (custom apps, scripts).** Return what we can; note that some custom-app discounts may not appear in this surface.
- **Steep upcoming discounts.** Flag visually (≥ 40% gets a ⚠).

## Examples

- [`examples/basic.md`](examples/basic.md)
