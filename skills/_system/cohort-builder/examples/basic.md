# Example — three cohort builds

## Pass-through (advanced merchant typed valid syntax)

```
spec: "vendor:Vendor A AND tag:summer"
kind: products

→ { filter: "vendor:Vendor A AND tag:summer",
    label:  "products from Vendor A tagged summer",
    kind:   "products",
    named_cohort: false }
```

## Named cohort

```
spec: "lapsed-vips"
kind: customers
shop_timezone: America/Denver
now: 2026-01-15

→ 6mo-ago resolves to 2025-07-15 in shop timezone
→ { filter: "tag:vip AND orders_count:>=2 AND updated_at:<'2025-07-15T00:00:00-07:00'",
    label:  "lapsed VIP customers (no activity in 6 months, ≥2 lifetime orders)",
    kind:   "customers",
    named_cohort: true }
```

## Bare-name footgun (rewritten safely)

```
spec: "Smith"
kind: customers

# Bare names match against notes, tags, addresses, company names — wrong people.
# Rewrite forced:

→ { filter: "first_name:Smith OR last_name:Smith",
    label:  "customers named Smith",
    kind:   "customers",
    named_cohort: false }
```
