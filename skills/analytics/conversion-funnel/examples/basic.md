# Example — conversion funnel

```
> Merchant: "Conversion funnel last 30 days."

example-shop · conversion funnel · last 30 days

                            Sessions    Step rate    Cum rate
  Sessions                     4,210
  → cart add                     840      20%          20%
  → reached checkout             420      50%          10%
  → completed checkout           210      50%           5%

Overall conversion: 5.0%
Drop-off pattern:  half drop off at cart → checkout (probably price/shipping shock),
                   half drop off at checkout → complete (probably payment friction).

Worth investigating: shipping costs, payment options.
```

Split by device:

```
> Merchant: "Funnel by device last 30 days."

example-shop · conversion funnel · last 30 days · split by device

  device:  desktop                Sessions    Step    Cum
    Sessions                         1,540
    → cart add                         420     27%    27%
    → reached checkout                 240     57%    16%
    → completed checkout               140     58%    9.1%

  device:  mobile                  Sessions    Step    Cum
    Sessions                         2,520
    → cart add                         380     15%    15%
    → reached checkout                 160     42%    6.3%
    → completed checkout                64     40%    2.5%

  device:  tablet                  Sessions    Step    Cum
    Sessions                           150
    → cart add                          40     27%    27%
    → reached checkout                  20     50%    13%
    → completed checkout                 6     30%    4.0%

Mobile is your weak point. Worth checking mobile checkout UX.
```
