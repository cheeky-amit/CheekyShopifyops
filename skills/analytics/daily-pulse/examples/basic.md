# Example — last 7 days

```
> You: "Sales last 7 days."

  example-shop · last 7 days (2026-01-09 to 2026-01-15) · USD

    Orders:        84       (+12% vs prior 7 days)
    Total sales:   $9,820   (+8%)
    Avg order:     $117     (-3%)
    Sessions:      4,210    (+22%)
    Conversion:    2.0%     (-0.4 pp)

  [time-series chart shown by your client]

  Want a deeper cut?
    • "top products last 7 days"
    • "where did sessions come from last 7 days"
    • "session funnel last 7 days"

— under the hood —
  Pulls daily totals across the range, computes the matching previous
  period for comparison, and renders both the summary and the trend.
```

Custom range:

```
> You: "Sales for 2026-01-01..2026-01-15."

  example-shop · 2026-01-01 to 2026-01-15 (15 days) · USD

    Orders:        178      (+9% vs prior 15 days)
    Total sales:   $20,940  (+11%)
    Avg order:     $117     (+2%)
    Sessions:      8,920    (+18%)
    Conversion:    2.0%     (-0.3 pp)
```

When you're on the Basic plan:

```
> You: "Sales last 30 days."

  example-shop · last 30 days · USD  (Basic plan — sales only)

    Orders:        420      (+9% vs prior 30 days)
    Total sales:   $48,200  (+12%)
    Avg order:     $115     (+3%)

  Sessions and conversion need Shopify plan or higher. Upgrade to see
  full traffic and funnel numbers here.
```
