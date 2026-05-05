# Stuck unfulfilled orders

Find paid orders that haven't shipped yet, oldest first.

> "Show stuck orders."
> "What hasn't shipped?"
> "Anything in the fulfillment queue that needs attention?"

## What it returns

Three groups:
- **Stale** — paid + unfulfilled + older than 72h (configurable).
- **Today** — paid + unfulfilled + within 72h.
- **Partial** — partially fulfilled (optional).

Each row shows order name, age, customer (first name + last initial), total, and line count.

## Privacy note

Customer names are abbreviated in this summary view (first name + last initial). Ask "show ORDER-1001" to see a specific order's full details.

## What this skill won't do

- Fulfill orders (no direct fulfillment tool; would need GraphQL `fulfillmentCreate`, which is a separate, careful workflow).
- Refund orders — refunds are blocked at the MCP level. Do those in Shopify admin.
