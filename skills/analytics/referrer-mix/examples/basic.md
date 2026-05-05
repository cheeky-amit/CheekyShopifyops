# Example — referrer mix

```
> You: "Where do sales come from?"

  example-shop · referrer mix · last 30 days · USD

    Source              Sessions    Orders    Sales       Conv
    ──────────────────────────────────────────────────────────
    direct                1,820        58     $6,820      3.2%
    search                1,420        42     $4,920      3.0%
    social                  920        24     $2,810      2.6%
    email                   320        18     $2,140      5.6%
    paid                    340        12     $1,420      3.5%

  Heads up: a chunk of "direct" is unattributable — deep links from apps,
  DMs and copy-pasted URLs (dark social), and browsers that strip the
  referrer all land here. Don't read this bucket as a campaign.

  Want a deeper cut? Ask "referrer mix by name last 30 days" — splits social
  into Instagram/Facebook, search into Google/Bing, and so on.

— under the hood —
  Pulls session and order totals grouped by traffic source for the window,
  then computes conversion per channel.
```

Drill-in:

```
> You: "Referrer mix by name last 30 days."

  example-shop · referrer mix by source and name · last 30 days

    Source     Name             Sessions   Orders    Sales      Conv
    ────────────────────────────────────────────────────────────────
    search     Google              1,210       38    $4,420    3.1%
    search     Bing                  210        4    $500      1.9%
    social     Instagram             520       16    $1,920    3.1%
    social     Facebook              280        6    $620      2.1%
    social     Pinterest             120        2    $270      1.7%
    email      (none)                320       18    $2,140    5.6%
    paid       Google                240       10    $1,180    4.2%
    paid       Meta                  100        2    $240      2.0%
    …

  Email converts best per session (5.6%). Worth seeing what your top
  email-driven products are: ask "top products from email last 30 days."
```
