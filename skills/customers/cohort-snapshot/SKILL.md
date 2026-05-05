---
id: customers.cohort-snapshot
name: cohort-snapshot
title: Customer cohort snapshot
description: Profile a named or custom customer cohort — count, average lifetime spend, geo split, marketing-consent rate.
audience: merchant
roles: [cmo, founder, cx]
category: customers
risk: read
mcp_tools:
  - get-shop-info
  - list-customers
graphql:
  query: false
  mutation: false
triggers:
  - "show me VIPs"
  - "snapshot of lapsed customers"
  - "who are my repeat buyers"
  - "customer cohort"
  - "describe my [cohort] customers"
inputs:
  - name: cohort
    type: string
    description: "Named cohort (vips, lapsed, repeat-buyers, high-value, lapsed-vips, subscribers, non-subscribers) or custom Shopify search."
outputs:
  summary: |
    Cohort label, count, average lifetime spend, top 3 countries, marketing-consent rate.
status: stable
version: 1.0.0
---

## Purpose

Merchants ask "who are my VIPs?" or "what does my lapsed cohort look like?" — they want a summary, not a CSV of names. This skill gives a compact profile.

## Workflow

0. **Load context.** Call `_system.shop-context`. The returned object includes `shop`, `operator`, `store`, `rituals`, `onboarding_state`, plus `needs_onboarding` and `onboarding_skipped` flags. If `needs_onboarding: true` and the merchant's ask isn't "set me up": invoke `onboarding.first-run` silently, complete it, then resume here. If `onboarding_skipped: true`: proceed with defaults (voice=`conversational`, write_defaults=`a`). Use `operator.voice` for verbosity, `operator.write_defaults` for confirm strictness, and `store.stage` to scale the pagination cap (500 default; 1000 on `100k-1m`/`1m+`).
1. Resolve `cohort` via `_system/cohort-builder` (kind: customers).
2. Read via `list-customers` paginated by `_system/data-extraction` (cap 500).
3. Compute aggregates:
   - count
   - average `total_spent`
   - country distribution (top 3)
   - marketing consent rate (% with `email_marketing_state == subscribed`)
4. Render summary. Do NOT list individual customers in this view (use `customers.high-value-list` for that).

## Output format

```
example-shop · cohort: VIPs (tag:vip)

  Count:           48
  Avg lifetime:    $XXX.XX
  Top countries:   US (32) · CA (9) · UK (4)
  Subscribed:      87%

To see specific customers: "show me top spending VIPs"
                          (uses customers.high-value-list)
```

## Edge cases

- **Empty cohort.** "No customers match `<cohort>`. Try a different filter."
- **>500 in cohort.** Surface that the snapshot is computed over the first 500 — for VIP-tier cohorts this should be rare; if hit, ask the merchant to narrow.
- **No location data.** Customers without addresses are excluded from country split; surface that count.

## Examples

- [`examples/basic.md`](examples/basic.md)
