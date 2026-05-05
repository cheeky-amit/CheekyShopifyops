# Where sales come from

A breakdown of your traffic and sales by channel — direct, search, social, email, paid — with a drill-in option to split each channel by the named referrer (Instagram vs Facebook, Google vs Bing, and so on).

## What you can say

> "Where do sales come from?"
> "Traffic sources last 30 days."
> "What's driving sales?"

## What you'll see back

A table of channels with sessions, orders, sales, and conversion rate for each. The "direct" channel will usually be the largest by sessions, and the skill always notes the catch underneath: a big share of "direct" isn't really direct. Deep links from apps, dark social shares (DMs, Slack, copy-pasted URLs), and browsers that strip the source all land in "direct" — so don't take that bucket at face value as a campaign or a strategy.

## What it won't do

- Won't compute profit or margin. Sales totals only.
- Won't tie sessions to specific marketing campaigns (no UTM-level breakdown in v1).
- Won't include test orders or cancelled orders.

## Settings you can change

- **Range** (default: last 30 days). Any window — the last 7 days, last 90 days, month-to-date, year-to-date, or a custom date span.
- **Drill-in** (default: off). Turn on to split each channel by named referrer (Instagram vs Facebook, Google vs Bing, etc.).

## Want to see under the hood

It pulls session and order totals grouped by traffic source for your window, computes conversion per channel, and surfaces the "direct is unattributable" caveat so the number isn't read as a campaign.

## Related

- [`analytics.daily-pulse`](../daily-pulse/) — overall sales trend for the same window.
- [`analytics.product-performance`](../product-performance/) — which products the channels were buying.
- [`analytics.conversion-funnel`](../conversion-funnel/) — where shoppers dropped off, optionally split by source.
- [`analytics.cohort-retention`](../cohort-retention/) — new vs returning mix across the window.
