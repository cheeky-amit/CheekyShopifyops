# Example — collection sale

```
> You: "Run 20% off Spring 2026 collection, Mar 1 to Mar 15."

  example-shop · plan a collection sale

    Collection:    "Spring 2026"  (32 products)
    Recent sales:  $4,820 in the last 30 days
    Percentage:    20% off
    Code:          SPRING2026-20  (auto-suggested — type your own to change)
    Live from:     2026-03-01 → 2026-03-15
    Minimum:       (none — say "minimum $50" or "minimum 2 items" if you want)

    Estimate: at this collection's recent pace, the 15-day window covers
              roughly $2,400 in topline. At 20% off, about $480 in margin
              given up.

  Heads up: once created, this code can't truly be deleted — Shopify doesn't
  allow it. After creation, your run log will include the exact command to
  end it immediately, copy-pasteable, in case the code goes wrong.

  Type "yes" to create.

> You: "yes"

  Done. SPRING2026-20 is live from 2026-03-01.

  To end this code immediately if it goes wrong — copy and run:
    [end-now command, ready to paste]

— under the hood —
  Resolves the collection by name, pulls recent sales for context,
  shows the plan for confirmation, then creates the code and emits the
  "end now" command for emergency use.
```

When the collection name is ambiguous:

```
> You: "Discount the Spring collection."

  Two collections match "Spring":
    • "Spring 2025"  (28 products, last sold 2025-06-12)
    • "Spring 2026"  (32 products, sold $4,820 in the last 30 days)

  Which did you mean?
```
