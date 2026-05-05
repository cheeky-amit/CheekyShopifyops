# Example — discount audit

```
> You: "What discounts do I have running?"

  example-shop · discount audit

  → ACTIVE NOW                                                               3

     SPRING20      20%        Collection: Spring 2026   ends 2026-03-15      42 uses
     WELCOME10     10%        Everyone / new customers  no end date          1,840 uses
     FREESHIP      free ship  Everyone / orders ≥ $50   ends 2026-02-28      124 uses

  → UPCOMING                                                                 1

     FLASH40       40%  ⚠     Everyone                  2026-02-14 → 2026-02-15

  → EXPIRED — hidden. Ask "audit including expired" to see them.

  Notes:
    • FLASH40 is steep (40%). Confirm intent before it goes live.
    • WELCOME10 has 1,840 uses and no end date. Worth checking whether it's
      still pulling its weight or quietly leaking margin.

— under the hood —
  Pulls every discount, sorts into the three buckets by date, and flags
  steep percentages so they don't slip past you.
```

Including expired:

```
> You: "Audit including expired."

  (... ACTIVE and UPCOMING as above ...)

  → EXPIRED (last 30 days)                                                   2

     HOLIDAY15     15%        Everyone                  ended 2025-12-31     320 uses
     BFRIDAY30     30%        Collection: Apparel       ended 2025-12-01     480 uses
```
