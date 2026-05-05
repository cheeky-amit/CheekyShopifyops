# Example — subscriber audit

```
> Merchant: "Audit my email subscribers."

example-shop · subscriber audit · cohort: all

  Total customers:    2,140

  Subscribed:         1,520  (71%)
  Pending (DOI):         28  (1%)
  Not subscribed:       540  (25%)
  Invalid:               42  (2%)       ← needs cleanup
  Redacted (GDPR):       10  (0.5%)

  Subscribed customers with ≥1 order:   1,201
  Orders → Subscriber conversion:       69%

The 42 invalid emails are likely bounces or typos. Two ways to handle them:
  1) Re-collect at next checkout (Shopify can prompt at checkout).
  2) Mark as not-subscribed manually in Shopify admin.

This skill won't auto-fix them — consent updates need a separate, careful workflow.
```
