# Email subscriber audit

A health check on your email list — who's in, who's pending, who's bouncing — and how well your buyers convert into subscribers.

## What you can say

> "Audit my email subscribers."
> "What's my consent rate?"
> "Any invalid emails on my list?"

## What you'll see back

A breakdown of every customer by their email-marketing state, plus a conversion number — what share of your buyers have actually opted in. The states, in plain words:

- **Subscribed** — opted in, fair game for marketing email.
- **Pending** — they signed up but haven't clicked the confirmation link yet (a "double opt-in" still in flight).
- **Not subscribed** — they exist as customers but never opted into marketing.
- **Invalid** — bounced, mistyped, or otherwise unreachable. Worth cleaning up.
- **Redacted** — customers who exercised their right to be forgotten. The audit counts them, never shows them.

## Privacy note

Redacted customers (GDPR or other right-to-be-forgotten requests) are counted only — never named, never enumerated. The audit shows aggregate buckets, not individuals. To look at one specific customer's consent, ask for them by name or email.

## What it won't do

- Won't change anyone's consent. Updating who's subscribed is sensitive — wrong move and you're spamming people who said no. That needs a separate, careful skill not in v1.
- Won't send re-confirmation emails — there's no email tool in this bundle.
- Won't fix invalid addresses for you. The audit flags them; the cleanup is your call.

## Settings you can change

- **Group of customers** (default: everyone). Limit by country, tag, or order count if you only want to audit a slice.

## Want to see under the hood

It pulls every customer's email-marketing state, counts each bucket, and computes the buyers-to-subscribers conversion as a single rate. No content of any individual record is shown.

## Related

- [`customers.cohort-snapshot`](../cohort-snapshot/) — group profile including consent rate at a glance.
- [`customers.high-value-list`](../high-value-list/) — see top spenders by name.
- [`retention.win-back-list`](../../retention/win-back-list/) — lapsed customers worth re-engaging.
- [`meta.wiring`](../../meta/wiring/) — how to connect an email tool if you want to actually send.
