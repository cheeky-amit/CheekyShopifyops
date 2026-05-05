# Build a win-back list

Lapsed customers worth re-engaging — defined as: ≥ N past orders, no activity for ≥ M months, subscribed to email.

> "Win-back list."
> "Lapsed customers."
> "Build a re-engagement list."

## Why these defaults

- **6 months lapsed** — recent enough that the brand is still familiar, old enough to count as "we lost them."
- **≥ 2 past orders** — filters one-time buyers, who are a different problem (acquisition cost, not retention).
- **Subscribed only** — you can email them legally. The skill flags if you turn this off.

## What this skill won't do

- Send the email/SMS. No email MCP in this bundle. See `meta.wiring`.
- Apply discount codes to the list. Use `promotions.discount-create` with `customer_segments` if you need a list-scoped discount.

## Privacy

Default view abbreviates names. Full names + emails only when you explicitly ask for the export view.
