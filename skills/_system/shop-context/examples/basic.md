# Example — load shop context (post-onboarding)

```
Caller: catalog.product-status-audit
Step:   0 (load context)

→ get-shop-info
→ graphql_query (ShopProfile, reads 4 merchant_ai.* metafields)

Returned:
  shop:
    name:         example-shop
    domain:       example-shop.myshopify.com
    email_domain: example.com
    currency:     USD
    timezone:     America/Denver
    country:      United States
    plan:         Shopify
    pretty:       example-shop · USD · America/Denver · Shopify plan

  operator:
    name:           Operator A
    role:           founder
    voice:          conversational
    write_defaults: a

  store:
    stage:          10k-100k
    model:          DTC apparel, US-only, ~120 SKUs
    discipline:     { tracks_inventory: true, single_location: true }
    last_refreshed: 2026-04-01T10:00:00Z

  rituals:
    monday_brief:     true
    weekly_brief_day: Monday

  onboarding_state:
    completed_at: 2026-04-01T10:00:00Z
    declined_at:  null
    version:      1.0

  needs_onboarding:   false
  onboarding_skipped: false

Caller now uses `shop.pretty` in its preview header:
  "Audit on example-shop · USD · America/Denver · Shopify plan"

Caller branches on profile:
  voice = conversational              → standard wording
  write_defaults = a                  → always preview + always confirm
  store.stage = 10k-100k              → use scaled threshold (e.g. low-stock = 10)
```

# Example — first run on a store (no profile yet)

```
Caller: orders.daily-pulse
Step:   0 (load context)

→ get-shop-info
→ graphql_query (ShopProfile)
   metafield_operator:   null
   metafield_store:      null
   metafield_rituals:    null
   metafield_onboarding: null

Returned:
  shop:               { ...basic profile... }
  operator:           null
  store:              null
  rituals:            null
  onboarding_state:   null
  needs_onboarding:   true
  onboarding_skipped: false

Caller behavior:
  needs_onboarding is true → invoke onboarding.first-run silently,
  complete it, then resume orders.daily-pulse.
```

# Example — merchant declined onboarding 5 days ago

```
Returned:
  shop:               { ...basic profile... }
  operator:           null
  store:              null
  rituals:            null
  onboarding_state:   { completed_at: null,
                        declined_at:  2026-04-30T14:22:00Z,
                        version:      1.0 }
  needs_onboarding:   false
  onboarding_skipped: true

Caller behavior:
  Use defaults: voice='conversational', write_defaults='a'
  (always preview / always confirm). Do NOT trigger onboarding.
```
