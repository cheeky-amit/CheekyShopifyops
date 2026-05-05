# Email marketing consent audit

Find out who's subscribed, who's pending, who's invalid, and how well orders convert to subscribers.

> "Audit my email subscribers."
> "What's my consent rate?"
> "Any invalid emails?"

## What it returns

Counts and shares for each email-marketing state: subscribed, pending, not subscribed, invalid, redacted (GDPR). Plus an order → subscriber conversion rate.

## What this skill won't do

- Update consent. That's a separate write skill (not in v1; possible via GraphQL `customerEmailMarketingConsentUpdate` and is sensitive — needs careful preview/confirm).
- Send re-confirmation emails (no email-sending tool in this bundle — see `meta.wiring`).
