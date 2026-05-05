# Example — shop snapshot

```
> You: "Shop snapshot."

example-shop

  Domain:        example-shop.myshopify.com
  Plan:          Shopify
  Currency:      USD
  Timezone:      America/Denver
  Country:       United States

  Catalog:       248 products · 18 collections
  Last 7 days:   84 orders · $X,XXX sales

What you can do next:
  • Today's sales:        "pulse"
  • Stuck orders:          "what hasn't shipped?"
  • Top sellers:           "top products"
  • What's running low:    "low stock"
  • Customer cohorts:      "show me my VIPs"
```

— under the hood —

It reads the shop profile (domain, plan, currency, timezone, country), counts products and collections, and totals orders and sales for the last 7 days.
