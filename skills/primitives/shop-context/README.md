# shop-context (primitive)

**Internal building block.** Merchants don't invoke this directly.

Loads the connected store's profile so domain skills can tailor output (currency formatting, timezone scheduling, plan-tier capability checks) and surface the store name in confirmation prompts.

## Used by

Almost every other skill. Recommended as step 1 of any read or write workflow.

## What it returns

```
{
  name:         "example-shop",
  domain:       "example-shop.myshopify.com",
  email_domain: "example.com",
  currency:     "USD",
  timezone:     "America/Denver",
  country:      "United States",
  plan:         "Shopify",
  pretty:       "example-shop · USD · America/Denver · Shopify plan"
}
```

## Privacy note

The merchant's full email address is intentionally *not* returned. Only the email domain. Skills never log, echo, or persist the full email anywhere.
