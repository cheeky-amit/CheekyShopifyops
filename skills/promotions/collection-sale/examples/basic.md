# Example — collection sale

```
> Merchant: "Run 20% off Spring 2026 collection, Mar 1 to Mar 15."

example-shop · plan a collection sale

  Collection:    "Spring 2026"  (32 products)
  Recent sales:  $4,820 in the last 30 days
  Percentage:    20% off
  Code:          SPRING2026-20  (auto-suggested — change with: code: <yours>)
  Validity:      2026-03-01 → 2026-03-15
  Minimum:       (none — set with minimum_purchase or minimum_quantity if you want)

  Estimate: at this collection's recent pace, the 15-day sale window covers roughly
            $2,400 in topline. At 20% off, ~$480 in margin will be given up.

  Once created, this can't be uncreated. To deactivate early, the run log will
  include the GraphQL operation to set endsAt = now.

  Type "yes" to create.

> Merchant: "yes"

→ create-discount(... percentage: 20, collectionId: ..., startsAt: 2026-03-01, endsAt: 2026-03-15)

Run: promotions.collection-sale @ 2026-01-15T14:32:11Z
Store: example-shop · USD · America/Denver · Shopify plan
Created: SPRING2026-20 (20% off, scoped to "Spring 2026", 2026-03-01 → 2026-03-15)

Deactivate early — copy and run:
  graphql_mutation:
    mutation { discountCodeBasicUpdate(
      id: "gid://shopify/DiscountCodeNode/000",
      basicCodeDiscount: { endsAt: "<now-iso>" }
    ) { ... } }
```
