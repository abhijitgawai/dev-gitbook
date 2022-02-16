# Credit

### Overview

Credit account is an isolated smart contract which behaves like decentralised wallet(custody) and collateral. Credit account could execute filtered financial orders, but do not provide direct access to funds held on it.

There are 2 contracts, which hold all policies: CreditManager.sol and CreditFilter.sol. CreditManager is responsible for core operations:

* Open credit account
* Close credit account
* Repay credit account
* Liquidate credit account
* Add collateral
* Borrow more

CreditFilter holds policies like allowed tokens and contracts, computing healthFactor and other.

Interacting with credit account



![](../../.gitbook/assets/Gearbox\_white\_high.004.png)

It's implemented as an isolated smart contract, traders can operate these contracts via Credit Manager (swap / farm etc. operations) but hasn't access to funds held on them.&#x20;

Credit Account acts as collateral and could be eliminated via Credit Manager by liquidators if it's health factor which represents ability to cover debt will be less than 1.&#x20;

### Core operations

![](../../.gitbook/assets/Gearbox\_white\_high.013.png)

Credit manager is responsible for operations with credit accounts such as:

* Open credit account
* Close credit account
* Repay credit account
* Liquidate credit account&#x20;
* Increase borrowing amount

Credit manager uses Credit filter for checking that operation is allowed.&#x20;

his pattern could be shown as:

![](../../.gitbook/assets/Gearbox\_white\_high.020.png)
