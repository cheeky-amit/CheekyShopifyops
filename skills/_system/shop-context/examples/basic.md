# Example — load shop context

```
Caller: catalog.product-status-audit
Step:   1 (load context)

→ get-shop-info

Returned:
  name:         example-shop
  domain:       example-shop.myshopify.com
  email_domain: example.com
  currency:     USD
  timezone:     America/Denver
  country:      United States
  plan:         Shopify
  pretty:       example-shop · USD · America/Denver · Shopify plan

Caller now uses `pretty` in its preview header:
  "Audit on example-shop · USD · America/Denver · Shopify plan"
```
