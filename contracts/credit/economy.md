# Economy

## Basic parameters

### Total Value&#x20;

Represents the Credit Account's balance in underlying asset.&#x20;

$$
TV(t)=\sum{\;c_i(t)*p_i(t)},
$$

where c\_i - balance of i-th asset in credit account,  p\_i - price of i-th asset calculated in underlying asset(from ChainLink oracle),&#x20;



### Threshold Weighted Value&#x20;

$$
TWV(t)=\sum{\;c_i(t)*p_i(t)*LT_i }
$$

where: \
c\_i - balance of i-th asset in credit account, \
p\_i - price of i-th asset calculated in underlying asset(from ChainLink oracle), \
LT\_i - liquidation threshold, the credit account manager constant showing the maximum allowable ratio of [Loan-To-Value](https://www.investopedia.com/terms/l/loantovalue.asp) for the i-th asset**.**&#x20;

### Health Factor

Represents the quality of credit account portfolio. Credit account can be liquidated if Hf <1.

$$
H_{f}(t) =\frac{TWV(t)}{b +interest\;accrued(t)}
$$

### Liquidation Threshold

Liquidation thresholds represents maximum allowable ratio of Loan-To-Value for the i-th asset (LTV is reciprocal of over-collaterization ratio of i-th asset).&#x20;

LT\_i for underlying asset is constant and equals:

$$
LT_U =100\% - Liquidation\;Premium -Liquidation\;Fee
$$

**How we define Liquidation Threshold?**

We use statistics of 5-min, 15-min, 1h change of i-th asset's price (price is in underlying asset) for last 180 days.&#x20;

### Interest rate accrued

$$
interest\;accrued(t)=b*\frac{CI(t)}{CI(t_k)}-b,\\\;\\
\text{where } t_k - \text {timestamp at opening credit account }
$$

## Operations

### Repay

Trader pays repay amount to protocol and the protocol transfers all funds from credit account to his account.&#x20;

$$
repay(t)=b+interest\;accrued(t)+\;fee\;amount(t)
$$

$$
interest\;accrued(t)=b*\frac{CI(t)}{CI(t_k)}-b
$$

where t\_k - Credit Account open timestamp.&#x20;

$$
fee\;amount(t) = (TV(t)-b-interest\;accrued(t))*fee + interest\;accrued(t) * fee2 ,
$$

where fee, fee2 - constant defined by Governance.

* Repay method transfers repay amount to pool
* Repay method calls pool's repay function with parameters borrow amount = b, profit = fee\_amount, shortage = 0.

## Liquidation

$$
Liq\;amount(t) = TV(t)*(1-liq\;discount)
$$

paid by liquidator.&#x20;

$$
Amount\;to\;pool(t) = \min\{Liq\;amount(t),b+interest\;accrued(t)+\;fee\;liq\;amount(t)\}
$$

$$
fee\;liq\;amount(t) = TV(t) * fee\;liq,
$$

where fee\_liq - constant defined by Governance.

$$
Remaining\;funds(t) = \max\{0,Liq\;amount(t)-Amount\;to\;pool(t)\}
$$

goes to trader.&#x20;

$$
PnL(t)=Amount\;to\;pool(t) -b-interest\;accrued(t)
$$

## Close account

Works like Liquidation, but before liquidation all funds from credit account are exchanged to underlying asset according to Uniswap invariant.&#x20;

## Increase borrow amount

User can borrow more funds from pool (increase borrow amount), if after this transaction, his Hf will be more than H\_fmin, which represents the lowest bound which is allowed for Increase borrowing. This Hf equals Hf which is set at opening credit account with max allowed leverage factor:

$$
H_{fmin}=LT_U*\frac{max\;leverage+1}{max\;leverage}
$$

To compute maximum amount which can be borrowed at t, you can use following formula:

$$
max\;increase\;borrow(t)=(b+interest\;accrued(t))\frac{H_f(t)-H_{fmin}}{H_{fmin}-LT_U}
$$

When user increases his borrowed amount on _db_ at moment _t_ Credit Account re-calculates credit account opening timepstamp on&#x20;

$$
\hat t_k: CI(\hat t_k)= \frac{b+db}{\frac{b}{CI(t_k)}+\frac{db}{CI(t)}}
$$

and re-write debt size on&#x20;

$$
\hat b = b + db
$$













