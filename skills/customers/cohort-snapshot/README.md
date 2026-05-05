# Customer cohort snapshot

A compact profile of a customer cohort — not a list of names, just the shape.

> "Show me my VIPs."
> "Snapshot of lapsed customers."
> "Who are my repeat buyers?"

## What it returns

- Count
- Average lifetime spend
- Top 3 countries
- Marketing-subscribed rate

## Named cohorts

`vips`, `lapsed`, `repeat-buyers`, `high-value`, `lapsed-vips`, `subscribers`, `non-subscribers`. Custom Shopify search syntax also accepted.

## What this skill won't do

- List individual customers → use `customers.high-value-list`.
- Update marketing consent → that requires GraphQL `customerEmailMarketingConsentUpdate`; covered by a future skill.
