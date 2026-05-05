# Example — first-run, full save

```
Merchant: "set me up"

Step 0 — load context
  shop-context returns: needs_onboarding: true, profile metafields all null.

Step 1 — auto-discovery (read-only sweep)
  Plan:                  Shopify
  Currency:              USD
  Timezone:              America/Denver
  Country:               United States
  Catalog size:          ~120 products
  Collections:           14
  Orders (last 30d):     ~80
  Returning rate (90d):  ~28%
  Locations:             1
  Tracking discipline:   5/5 sampled products track inventory
  Inferred stage:        10k-100k
  Inferred model:        DTC apparel, US-only, ~120 SKUs, single location

Step 2 — read-back + questions
  Claude:
    "Here's what I learned about your store:
       • Plan: Shopify, USD, MST timezone, US.
       • ~120 products, 14 collections, ~80 orders in the last 30 days.
       • ~28% returning customers (last 90 days).
       • 1 location. All sampled products track inventory.
     A few quick questions so I can be useful:

     1) Who am I talking to, and what's your role?
        (Examples: 'Amit, founder' / 'Sara, ops' / 'Joe, marketing')
     2) Roughly what stage is the store in?
        (just-launched / under-10k-mo / 10k-100k / 100k-1m / 1m+)
     3) When you ask me to change something — what's your default?
        (a) Always preview, always wait for 'yes' even on small changes.
        (b) Preview, but if you say 'do it' assume yes.
        (c) For big or bulk changes, always confirm; for small ones, just do it.
     4) Want a Monday-morning brief each week?
        (yes-Monday / yes-other-day / no)"

Merchant:
  "1) Operator A, founder. 2) 10k-100k. 3) a. 4) yes-Monday."

Step 3 — preview
  Claude:
    "Profile preview:
       Operator:
         Name:           Operator A
         Role:           founder
         Voice:          conversational  (default; we'll learn yours over time)
         Write defaults: a — always preview, always wait for 'yes'
       Store:
         Stage:          10k-100k
         Model:          DTC apparel, US-only, ~120 SKUs, single location
         Discipline:     tracks inventory, single location, has collections
         Last refreshed: 2026-05-05T14:00:00Z
       Rituals:
         Weekly brief:   Monday
       Onboarding state:
         Completed:      2026-05-05T14:00:00Z
         Version:        1.0

     Save this to your store under merchant_ai.* metafields?
     Type 'yes' to save, 'edit' to change something, 'no' to skip and use defaults.

     Heads-up: after you say yes, Shopify itself will ask you to confirm the
     save once more — that's normal."

Merchant: "yes"

Step 4 — execute
  → graphql_helper validates SaveProfile mutation
  → graphql_mutation metafieldsSet (host prompts merchant for write confirmation)
  Merchant approves the host prompt.

  Response:
    metafieldsSet returns:
      metafields: [
        { id: gid://shopify/Metafield/000, namespace: merchant_ai, key: operator_profile, value: "..." },
        { id: gid://shopify/Metafield/001, namespace: merchant_ai, key: store_profile,    value: "..." },
        { id: gid://shopify/Metafield/002, namespace: merchant_ai, key: rituals,          value: "..." },
        { id: gid://shopify/Metafield/003, namespace: merchant_ai, key: onboarding_state, value: "..." }
      ]
      userErrors: []

Step 5 — confirm save
  Claude: "Saved. You're all set. What do you want to work on?"
```
