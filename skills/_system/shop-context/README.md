# shop-context

**Internal building block.** Merchants don't invoke this directly.

Loads two things in one pass:

1. **Store profile** — currency, timezone, country, plan, name. Used for formatting, capability checks, and the `pretty` line that domain skills show in their previews.
2. **Merchant profile** — saved under shop-level metafields in namespace `merchant_ai` by the onboarding skills. Captures who's running the store, how they want Claude to behave, and what stage the business is at.

Together they tell every other skill (a) which store and (b) how to talk and act on it.

## Used by

Almost every other skill. Recommended as step 0 of any read or write workflow.

## What it returns

```
{
  shop: {
    name:         "example-shop",
    domain:       "example-shop.myshopify.com",
    email_domain: "example.com",
    currency:     "USD",
    timezone:     "America/Denver",
    country:      "United States",
    plan:         "Shopify",
    pretty:       "example-shop · USD · America/Denver · Shopify plan"
  },

  operator: {                             // null if not yet onboarded
    name:           "<operator-name>",
    role:           "founder",
    voice:          "conversational",     // 'terse' | 'conversational' | 'explained'
    write_defaults: "a"                   // 'a' | 'b' | 'c'
  },

  store: {                                // null if not yet onboarded
    stage:          "10k-100k",
    model:          "<one-line model>",
    discipline:     { /* signals */ },
    last_refreshed: "2026-04-01T10:00:00Z"
  },

  rituals: {                              // null if not yet onboarded
    monday_brief:     true,
    weekly_brief_day: "Monday"
  },

  onboarding_state: {                     // null if no onboarding has ever been attempted
    completed_at: "2026-04-01T10:00:00Z",
    declined_at:  null,
    version:      "1.0"
  },

  needs_onboarding:   false,              // true → caller should hand off to onboarding.first-run
  onboarding_skipped: false               // true → caller should use defaults; do NOT trigger onboarding
}
```

`write_defaults` codes:
- `a` — always show a preview and wait for "yes" even on small changes.
- `b` — show a preview, but if the merchant says "do it" assume yes.
- `c` — for big or bulk changes, always confirm. For small ones, just do it.

## Privacy note

The merchant's full email address is intentionally *not* returned. Only the email domain. Skills never log, echo, or persist the full email anywhere. The `merchant_ai.*` metafields hold operator name and role, which are merchant-controlled — they're saved on the merchant's own store, not anywhere else.
