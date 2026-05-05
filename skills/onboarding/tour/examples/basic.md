# Example — tour, no profile saved yet

```
Merchant: "what can you do?"

Step 0 — load context
  shop.pretty: example-shop · USD · America/Denver · Shopify plan
  onboarding_state: null
  needs_onboarding: true

Step 1 — render tour
  Claude:
    "Here's what I can do on example-shop · USD · America/Denver · Shopify plan.

     **catalog** — Audit and clean up your product catalog.
       Try: 'audit my products' · 'find products missing descriptions' · 'find duplicate products'

     **inventory** — Track and reconcile what's in stock.
       Try: 'what's running low?' · 'do a cycle count of my warehouse' · 'I just received a shipment'

     **orders** — Read-only views of orders.
       Try: 'yesterday's orders' · 'what's stuck unfulfilled?' · 'find an order from Customer A'

     **customers** — Read-only customer cohorts and lists.
       Try: 'show me my VIPs' · 'who's my top spender?' · 'audit marketing consent'

     **promotions** — Create and audit discounts.
       Try: 'create a 10% sale' · 'what discounts are running?' · 'discount the Summer collection'

     **analytics** — Sales, products, referrers, funnel, cohorts.
       Try: 'today's sales' · 'best and worst sellers' · 'where are my customers coming from?'

     **retention** — Win-back and anniversary cohorts.
       Try: 'list lapsed customers' · 'who's near their first-order anniversary?'

     **storefront** — Shop snapshot and new-store previews.
       Try: 'what plan am I on?' · 'preview a new store from a description'

     **reporting** — End-of-week brief.
       Try: 'send me a weekly brief'

     Want to set up properly? Say 'set me up'."
```

# Example — tour, profile already saved

```
Merchant: "show me around"

Step 0 — load context
  onboarding_state: { completed_at: 2026-04-01T10:00:00Z, declined_at: null, version: "1.0" }
  needs_onboarding: false
  onboarding_skipped: false

Step 1 — render tour
  ...same nine category blocks...

CTA at the end:
  "All set up. What do you want to work on?"
```
