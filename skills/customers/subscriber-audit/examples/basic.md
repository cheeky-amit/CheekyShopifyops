# Example — subscriber audit

```
> You: "Audit my email subscribers."

  example-shop · subscriber audit · everyone

    Total customers:        2,140

    Subscribed:             1,520   (71%)
    Pending confirmation:      28   (1%)
    Not subscribed:           540   (25%)
    Invalid:                   42   (2%)        ← worth cleaning up
    Redacted (GDPR):           10   (0.5%)

    Subscribed customers with at least one order:   1,201
    Buyers → subscribers conversion:                69%

  The 42 invalid emails are likely bounces or typos. Two ways to handle them:
    1) Re-collect at next checkout (Shopify can prompt for an email there).
    2) Mark each as not-subscribed manually in your Shopify admin.

  This skill won't auto-fix consent — that's a separate, careful workflow.

— under the hood —
  Pulls every customer's marketing state, counts each bucket, and computes
  the buyer-to-subscriber rate as one number.
```

Filtered slice:

```
> You: "Audit subscribers in Canada."

  example-shop · subscriber audit · customers in Canada

    Total customers:        320

    Subscribed:             198   (62%)
    Pending confirmation:     4   (1%)
    Not subscribed:         108   (34%)
    Invalid:                  8   (3%)
    Redacted (GDPR):          2   (0.6%)

  Canadian consent rate is below your store-wide rate (71%). Worth a look at
  what's different at checkout for that audience.
```
