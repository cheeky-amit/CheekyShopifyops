# Snapshot of a group of customers

A quick profile of a group of customers — say all your VIPs, or everyone who hasn't bought in six months — so you can see the shape of the group, not a list of names.

## What you can say

> "Show me my VIPs."
> "Snapshot of lapsed customers."
> "Who are my repeat buyers?"

## What you'll see back

A short profile of the group: how many people are in it, their average lifetime spend, the top three countries they're in, and what share have opted into your marketing emails. No individual names — for that, ask for the top spenders by name.

## What it won't do

- Won't list individual customers. For names, use the top customers list.
- Won't update marketing consent. Changing who's subscribed is sensitive and needs a separate, careful skill (not in v1).
- Won't email anyone — there's no email tool in this bundle.

## Settings you can change

- **Group of customers** (default: everyone). Shorthand names work — `vips`, `lapsed`, `repeat-buyers`, `high-value`, `lapsed-vips`, `subscribers`, `non-subscribers`. You can also describe the group in plain words and the skill will translate.

## Want to see under the hood

It pulls a slice of customers matching your group, computes the count, average lifetime spend, country split, and consent rate, and shows them as one compact profile. No individual records leave the read step.

## Related

- [`customers.high-value-list`](../high-value-list/) — see the actual top spenders, with names.
- [`customers.subscriber-audit`](../subscriber-audit/) — break down who's subscribed to email vs not.
- [`analytics.cohort-retention`](../../analytics/cohort-retention/) — how new vs returning customers trend over time.
- [`retention.win-back-list`](../../retention/win-back-list/) — pull a list of lapsed customers worth re-engaging.
