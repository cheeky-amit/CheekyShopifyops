# Example — create a discount

```
> Merchant: "Create a 20% off code SPRING20 for the Spring 2026 collection,
             valid Mar 1 to Mar 15. Minimum order $50."

example-shop · create discount

  Title:        Spring Sale
  Code:         SPRING20
  Percentage:   20% off
  Scope:        Collection "Spring 2026"  (32 products)
  Customers:    All
  Minimum:      Order subtotal ≥ $50
  Validity:     2026-03-01 → 2026-03-15

  Once created, this can't be uncreated. To deactivate early, you'd set
  endsAt to now via GraphQL — I'll include that operation in the run log.

  Type "yes" to create.

> Merchant: "yes"

→ create-discount(...)

Run: promotions.discount-create @ 2026-01-15T14:32:11Z
Store: example-shop · USD · America/Denver · Shopify plan
Created: SPRING20 (20% off, scoped to "Spring 2026", min $50, 2026-03-01 → 2026-03-15)

To deactivate early — copy and run:
  graphql_mutation:
    mutation { discountCodeBasicUpdate(
      id: "gid://shopify/DiscountCodeNode/000",
      basicCodeDiscount: { endsAt: "<now-iso>" }
    ) { ... } }
```

Steep-discount confirmation:

```
> Merchant: "Create a 70% off code BLACKFRIDAY..."

  70% is steep — that's near the floor of profitability for most products.
  Confirm? Or did you mean 7%?
```
