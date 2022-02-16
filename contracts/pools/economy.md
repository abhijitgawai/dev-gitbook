# Pool economy

Capital is required for traders to get leverage on the platform. For this, there are Liquidity Pools: anyone can become a liquidity provider by depositing funds in the Liquidity Pool.&#x20;

The profitability of LPs depends on the pool utilization ratio - the higher utilization, the higher interest rate. Each pool has an underlying asset and risk parameters such as: allowed for trading tokens list, allowed DEXes, stable coin pool, and others.

In the current version, we implement linear extrapolation for interest rate calculation as Aave did, in v2 we are going to use a specially designed curve - see the [paper](https://colab.research.google.com/drive/1UciFX7BQ62mtme0r84FO95FfA8C\_zQKI).

### Diesel tokens

Each pool has its own diesel (LP) tokens. Each time, when a liquidity provider adds money to the pool, he gets diesel tokens back (like c-tokens in Compound).&#x20;

$$
rate = \frac{expected\;liquidity}{diesel\;tokens\;supply}
$$

Liquidity providers get profits from holding diesel tokens cause they grow with expected interest. LP can keep diesel tokens on their wallets and then withdraw the deposit + interest or can use them as collateral in lending protocols or even sell them on the secondary market.\
\
Diesel tokens are 100% liquid yield-generating assets.

## Basic parameters

* EL(t) - [expected liquidity](economy.md#expected-liquidity)&#x20;
* B(t) - total borrowed
* r(t) - borrow rate
* d(t) - diesel rate
* CI(t) - cumulative index (it shows value of money at moment t)

### **Periods and timestamp**

All functions are piecewise linear functions. Each change in available liquidity  or borrowed amount [updates rate parameters](economy.md#rate-parameters-update). In follow formulas we use the convention:

$$
t_n - current\;timestamp,\\t_{n-1} - timestamp\;of\;last\;rate\;update
$$

### Available liquidity

The amount of money available in pool.&#x20;

### EL(t) - Expected Liquidity  <a href="#expected-liquidity" id="expected-liquidity"></a>

The amount of money should be in the pool if all users close their Credit Accounts and return debt

$$
EL(t_{n})= EL(t_{n-1})+B(t_{n-1})*r(t_{n-1})*(t_{n}-t_{n-1})
$$

### B(t) - Total borrowed <a href="#total-borrowed" id="total-borrowed"></a>

Represents total borrowed amount without accrued interest rate:

$$
B(t) = \sum b_i
$$

### r(t) - Borrow APY <a href="#borrow-apy" id="borrow-apy"></a>

Represents current borrow APY. Depends on pool utilisation parameter and computed independently using [Interest rate model](linearinterestratemodel.md).



### d(t) Diesel rate <a href="#diesel-rate" id="diesel-rate"></a>

Liquidity providers get profits from holding diesel tokens cause they grow with expected interest. LP can keep diesel tokens on their wallets and then withdraw the deposit + interest.

Diesel Rate is the price of Diesel token (LP token).&#x20;

$$
d(t) = \frac{EL(t)}{diesel\;supply(t)}, \text{if diesel supply >0}\\\;\\\text{d(t) = 1, if diesel supply is 0}
$$

### Cumulative Index

Cumulative Index is aggregated variable that shows value of borrowing money.&#x20;

$$
CI(t_{n})=CI(t_{n-1})(1+r(t_{n-1})*(t_{n}-t_{n-1})),
$$

$$
r(t_{n})=calc\;interest\;rate(EL(t_{n}), available\;liquidity(t_n))
$$

## Rate parameters update

Updates borrow rate & cumulative index. Called each time when borrowed amount or available liquidity is changed:

* Add liquidity
* Remove liquidity
* Credit account manager lends money
* Credit account manager repays debt

$$
CI(t_{n})=CI(t_{n-1})(1+r(t_{n-1})*(t_{n}-t_{n-1})),
$$

$$
r(t_{n})=calc\;interest\;rate(EL(t_{n}), available\;liquidity(t_n))
$$

## Pool operations

### Add liquidity

$$
EL(t_n) = EL(t_{n-1})+amount_U
$$

$$
mint\;diesel\;tokens =amount_{U}*\frac{EL(t_n)}{diesel\;supply(t_n)}
$$

where amount\_U - is amount of added underlying liquidity.  Then called Pool Update().&#x20;

### Remove liquidity

At&#x20;

$$
EL(t_n) = EL(t_{n-1})+
$$

$$
+B(t_{n-1})*r(t_{n-1})*(t_{n}-t_{n-1})-amount_{LP}*\frac{diesel\;supply(t_n)}{EL(t_{n-1})},
$$

$$
burn\;diesel\;tokens =amount_{LP}
$$

where amount\_LP - amount of removed LP tokens.Then call Pool Update().&#x20;



### Lend Credit Account

$$
B(t_n) = B(t_{n-1})+amount_B
$$

where amount\_B - borrowed amount. Then Pool Update().&#x20;



### Repay Credit Account

PnL - is result of VA repaying:

$$
PnL = total\;funds - amount_B - interest
$$

Updating total borrowed amount:

$$
B(t_n) = B(t_{n-1})-amount_B
$$

**PnL>=0:**

This case means, that return value > borrowed amount +  expected interest accrued. Interest accrued is already included in expected liquidity. At this point, the protocol keeps all funds in the pool and mint diesel tokens to treasury fund.

$$
EL(t_n) = EL(t_{n-1})+PnL
$$

$$
amount\;minted\;to\;treasury = \frac{PnL}{diesel\;rate(t_{n})}
$$

**PnL<0:**

This case means, that returned value < borrowed amount + expected interest rate. At this cases, pool uses treasury fund as insurance and burn tokens to keep _diesel rate_ on the same level.&#x20;

$$
amount\;to\;burn\;from\;treasury = min(treasury\;balance, \frac{|PnL|}{diesel\;rate(t_n)})
$$

$$
EL(t_n) = EL(t_{n-1})-|PnL|+amount\;to\;burn\;from\;treasury*diesel\;rate(t_n)
$$

### Fees distribution

Each time the trader/farmer closes the credit account, he pays back to the pool:

* Borrowed amount
* Interest accrued
* Fees

The fees remain in the pool, instead, the treasury receives the pool's LP tokens. The protocol has a feature to leave part of the fees in the pool, thereby increasing the price of diesel tokens, that is, increasing the APY of the pool.

### Insurance and rebalancing

In some rare cases, the remaining funds after paying liquidation premium could be less than borrowed amount + interest rate + fee. In this case, the protocol uses treasury to compensate for the shortage by burning diesel tokens to keep the diesel rate as it should be.

Let's consider an example:

1\) LP adds 1000 DAI to the pool. Diesel rate =1, he got 1000 dDAI (diesel DAI tokens). Total liquidity = 2000, and diesel supply = 2000.

2\) Trader borrowed 1000 DAI and used them for some period. Let's assume that interest accrued is 100DAI. So, at time of return: total liquidity = 2100, diesel rate= 2100 / 2000 = 1.05

3\)  LP earns on diesel rate, so their interest rate income is already accounted in 1.05 rate.

4\) Trader's account was liquidated, and the remaining funds are 1000 DAI only. So, if there is no insurance fund was there, the diesel rate should return to 1 which means that LP earns nothing. However, in this case, protocol burns treasure's tokens to keep the diesel rate on the same level:

$$
token\;to\;burn=  2000\;*(1.05-1)/1.05 \approx 95.23
$$

5\) Let's check that burning 95.23 will keep diesel rate as it was before this accident:

$$
diesel\;rate = \frac{2000}{2000-95.23} \approx1.05
$$

So, in this case, the treasury was used to cover some losses and behave like an insurance fund.



