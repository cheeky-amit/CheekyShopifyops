# Example — VIP snapshot

```
> You: "Show me a snapshot of my VIPs."

  example-shop · group: VIPs (anyone tagged "vip")

    Count:           48
    Avg lifetime:    $462.00
    Top countries:   US (32) · CA (9) · UK (4)
    Subscribed:      87%

  Want names? Ask "top spending VIPs."
  Want to compare? Ask "snapshot of non-VIP repeat buyers."

— under the hood —
  Resolves the group, pulls those customers a slice at a time, then shows
  count, average spend, country split, and consent rate as one compact view.
```

When the group is empty:

```
> You: "Snapshot of lapsed VIPs."

  example-shop · group: lapsed VIPs (tagged vip, 2+ orders, no activity in 6 months)

    Nobody fits. Either your VIPs are still active, or you don't tag VIPs yet.
    To start tagging VIPs, add a "vip" tag to top-spending customers.
```
