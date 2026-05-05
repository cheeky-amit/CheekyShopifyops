# Active discount audit

List discount codes — active, upcoming, and (optionally) recently expired.

> "What discounts do I have running?"
> "Audit my discount codes."
> "Any active sales?"

## What it returns

Bucketed by status:
- **Active now** — currently usable.
- **Upcoming** — scheduled but not yet started.
- **Expired** — past `endsAt` (only included if you ask).

Each row shows the code, type (percentage/free-shipping/BXGY), scope, validity, and redemption count.

## Implementation note

This skill uses GraphQL (`discountNodes`) — there's no direct tool for listing discounts. The skill goes through the standard validate-then-execute workflow.

## What this skill won't show

- Custom-app discounts (Shopify Functions, app-specific scripts) may not appear in `discountNodes`. The skill notes this if your store has discount apps installed.
