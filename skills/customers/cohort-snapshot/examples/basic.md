# Example — VIP snapshot

```
> Merchant: "Show me a snapshot of my VIPs."

example-shop · cohort: VIPs (tag:vip)

  Count:           48
  Avg lifetime:    $462.00
  Top countries:   US (32) · CA (9) · UK (4)
  Subscribed:      87%

To see specific customers: ask "top spending VIPs".
To compare with non-VIPs:  ask "snapshot of non-VIP repeat buyers".
```

Empty case:

```
> Merchant: "Snapshot of lapsed VIPs."

example-shop · cohort: lapsed VIPs (tag:vip AND orders_count:>=2 AND no activity 6mo)

  Nobody fits. Either your VIPs are still active, or you don't tag VIPs yet.
  To start tagging VIPs, add a `vip` tag to top-spending customers.
```
