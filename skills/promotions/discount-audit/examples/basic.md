# Example — discount audit

```
> Merchant: "What discounts do I have running?"

example-shop · discount audit

→ ACTIVE NOW                                                                  3

   SPRING20      20%       Collection: Spring 2026     ends 2026-03-15        42 redemptions
   WELCOME10     10%       All / new customers         no end                 1,840 redemptions
   FREESHIP      FREE-SHIP All / orders ≥ $50          ends 2026-02-28        124 redemptions

→ UPCOMING                                                                    1

   FLASH40       40% ⚠     All                         2026-02-14 → 2026-02-15

→ EXPIRED (not shown — ask "audit including expired" to see them)

Notes:
  • FLASH40 is steep (40%). Confirm intent before it goes live.
  • WELCOME10 has 1,840 redemptions and no end date — check if it's still
    pulling its weight or just leaking margin.
```

Including expired:

```
> Merchant: "Audit including expired."

(... ACTIVE and UPCOMING as above ...)

→ EXPIRED (last 30 days)                                                      2

   HOLIDAY15     15%       All                         expired 2025-12-31     320 redemptions
   BFRIDAY30     30%       Collection: Apparel         expired 2025-12-01     480 redemptions
```
