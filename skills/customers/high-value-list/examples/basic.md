# Example — top spenders

```
> Merchant: "Top 10 customers."

example-shop · top 10 by spend

   1.  Customer A B.   $4,820.00   23 orders   US
   2.  Customer C D.   $3,210.00   18 orders   CA
   3.  Customer E F.   $2,940.00   12 orders   US
   4.  Customer G H.   $2,580.00   15 orders   US
   5.  Customer I J.   $2,210.00   8 orders    UK
   6.  Customer K L.   $1,940.00   11 orders   US
   7.  Customer M N.   $1,720.00   14 orders   AU
   8.  Customer O P.   $1,540.00   6 orders    US
   9.  Customer Q R.   $1,380.00   9 orders    CA
  10.  Customer S T.   $1,210.00   7 orders    US

To see one in full: ask "show Customer A B." or "show #1".
```

Filtered cohort:

```
> Merchant: "Repeat buyers in Canada."

example-shop · top 20 by spend · cohort: country:CA AND orders_count:>=2

   1.  Customer C D.   $3,210.00   18 orders   CA
   2.  Customer Q R.   $1,380.00   9 orders    CA
   3.  Customer U V.   $980.00     5 orders    CA
   …
```
