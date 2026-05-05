# Example — create a discount

```
> You: "Create a 20% off code SPRING20 for the Spring 2026 collection,
        valid Mar 1 to Mar 15. Minimum order $50."

  example-shop · create discount

    Title:        Spring Sale
    Code:         SPRING20
    Percentage:   20% off
    Applies to:   Collection "Spring 2026"  (32 products)
    Customers:    Everyone
    Minimum:      Order subtotal ≥ $50
    Live from:    2026-03-01 → 2026-03-15

  Heads up: once created, this code can't truly be deleted — Shopify doesn't
  allow it. But you CAN end it instantly. After creation, your run log will
  include the exact command to set its end date to right now, copy-pasteable.

  Type "yes" to create.

> You: "yes"

  Done. SPRING20 is live from 2026-03-01.

  To end this code immediately if it goes wrong — copy and run:
    [end-now command, ready to paste]

— under the hood —
  Validates the plan, shows it for your confirmation, then creates the code
  and emits the "end now" command for emergency use.
```

Steep-discount confirmation:

```
> You: "Create a 70% off code BLACKFRIDAY..."

  Heads up: 70% is steep — that's near the floor of profitability for most
  products, and quite often a typo. Did you mean 7%?

  Type "yes, 70 is correct" to keep it. Type "yes, change to 7" to fix.
```
