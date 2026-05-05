# First-order anniversary cohort

Customers whose first order falls inside an anniversary window (default: 1 year ± 14 days).

> "First-order anniversary."
> "Who's coming up on their one-year with us?"
> "Two-year anniversary cohort."

## Why this matters

The anniversary of a first order is a low-friction moment for a "thank you / come back" touch. The cohort is naturally small but tends to convert well on retention emails.

## Implementation note

`list-customers` doesn't filter on first-order date directly. The skill uses GraphQL (`customers.orders(first: 1, sortKey: PROCESSED_AT)`) to find each customer's first order, then filters in-skill.

## Sends

This skill builds the list — it doesn't send. Email/SMS sends need an email MCP. See `meta.wiring`.
