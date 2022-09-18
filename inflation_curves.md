# Zero coupon inflation swaps (ZCIS)

## Payoff
In ZCIS fixed a *single* fixed cash-flow is exchanged for *single* inflation cash-flow. If ZCIS has a strike $K$, and maturity $T$, the cashflow exchange is:
- fixed: $(1+K)^T-1$
- float: $I(T)/I(0)-1$

## The inflation index $I$
The inflation index $I$ that is used in the payoff **is a lagged version of a CPI**. Hence, there is a `lag`, which is typically 3 months (US, EU market), or 2 months (UK).

Plus also note, CPI is typically published with a small delay (usually two weeks), so Aug' CPI is published in mid Sep.

To obtain $I$`(yyyy-mm-dd)` we need to take into account CPI value that is lagged and interpolated.

The interpolated value would be computed by first assigning index values:

```
I(yyyy-mm-01)<-CPI(yyyy-(mm-lag)), and 
I(yyyy-(mm+1)-01)<-CPI(yyyy-(mm-lag+1))
```

For example when we want to compute `I(2004-05-12)`, assuming 3-month lag we need to know:
- `CPI(2004-02)`,
- `CPI(2004-03)`

Let's assume the values are:
- `CPI(2004-02)=186.20`,
- `CPI(2004-03)=187.40`

Then:
- `I(2004-05-01)=CPI(2004-02)=186.20` and
- `I(2004-06-01)=CPI(2004-03)=187.40`.

Given these two edge values of `I` the interpolated value of  `I(2004-05-12)` is `186.20+(5-1)/31*(187.40-186.20)`.


## Building the CPI curve from ZCIS
Given a ZCIS quote $K$ for a term $T$, we can obtain $I(T)$ by matching the fixed and inflation leg payoffs.

This means:
$$\frac{{I(T)}}{{I(0)}} - 1 = {(1 + K)^T} - 1,$$
from which we can imply $I(T)$ as
$$I(T) = I(0){(1 + K)^T}.$$

If today (when we observe the market quotes) is a date $t=0$`=yyyy-mm-dd`, we note that $I(0)$ is not the 'current' CPI but needs to be computed from `CPI(yyyy-(mm-lag))` and `CPI(yyyy-(mm-lag+1))`.
The computed $I(T)$ then represents `CPI(T-lag)`. 
Hence, from a 10Y ZCIS we typically infer a quote such as `CPI(9Y9M)` because we know that `I(T)` would be based on CPI few months earlier.

**Note that**  
ORE displays in `flows.csv` `CPI` values, not `I`.

