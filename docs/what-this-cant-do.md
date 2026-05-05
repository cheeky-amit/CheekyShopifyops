# What this can't do

This bundle is strictly about Shopify. Anything off-Shopify — email, ads, accounting, supplier orders — needs another tool. Here's the short list.

## Blocked at the Shopify level

| Want to do | Where to do it instead |
|---|---|
| Refund or cancel an order | Orders → [order] → Refund/Cancel in Shopify admin |
| Issue a gift card | Products → Gift cards |
| Publish or edit your live theme | Online store → Themes |
| Add or remove staff | Settings → Users |

The skills know about these and will tell you if you ask.

## Needs another tool

| Want to do | What you'd need |
|---|---|
| Send an email or SMS to customers | An email/SMS tool (Klaviyo, Mailchimp, Resend, Twilio, etc.) connected separately |
| Place a purchase order with a supplier | Your supplier or 3PL workflow |
| Sync sales to accounting | An accounting tool (QuickBooks, Xero) |
| Run an ad campaign | An ads platform (Meta, Google, TikTok) |
| Track ROAS or attribution | An attribution tool (Polar, Triple Whale, etc.) |
| Customer support tickets | A help-desk tool (Gorgias, Zendesk) |

To combine this bundle with another tool, see [`../skills/meta/wiring/README.md`](../skills/meta/wiring/README.md).

## Out of scope by design

- B2B / wholesale workflows (v1 is DTC-focused).
- Subscription billing (needs the subscription app's own connector).
- Custom-app discounts (some advanced discount types may not appear in the discount audit).
- Translations / i18n.
- Markets / multi-currency price lists.

These may land in v2 if there's demand.
