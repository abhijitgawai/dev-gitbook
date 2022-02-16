# Extracting data

## Contracts discovery

At the first this you have to do is to get [AddressProvider](../contracts/core/addressprovider.md) to start contract discovery. This address provider can be hard coded in your app.&#x20;

Then, you can get [contractsRegister](../contracts/core/poolregistry.md) contact to discover [pool](../contracts/pools/pool-service.md) & [virtual account manager](../contracts/credit/) contracts.

To do that use that use following methods:

* [getPoolsCount](../contracts/core/poolregistry.md#getpoolscount)  to get quatity of pools
* [pools(uint id)](../contracts/core/poolregistry.md#pools-uint-id) to get pool address by id (usually in cycle)
* [getVirtualAccountManagersCount](../contracts/core/poolregistry.md#getvirtualaccountmanagerscount) to get quanity of connected pools
* [virtualAccountManagers](../contracts/core/poolregistry.md#virtualaccountmanagers) to get vam address by id

## Liquidity providers side

All liqudity operations and pool data are available via[ PoolService](../contracts/pools/pool-service.md) contract. To manage liquidity you can use following methods:

* [addLiquidity](../contracts/pools/pool-service.md#addliquidity) to add liquidty and get diesel(LP) tokens
* [removeLiquidity](../contracts/pools/pool-service.md#removeliquidity) to remove liquidty and burn diesel(LP) tokens

For ETH operations, use [WETHGateway](broken-reference), which takes all needed thinks to convert assets.

To get pool parameters, you can use getters:

* [expectedLiquidity](../contracts/pools/pool-service.md#expectedliquidity) to get current [expected liquidity](../contracts/pools/economy.md#expected-liquidity)
* [availableLiquidity](../contracts/pools/pool-service.md#availableliquidity) to get available liquidity. Do not forget check if you're going to open virtual account that pool ha enough funds
* [borrowAPY\_RAY](../contracts/pools/economy.md#borrow-apy) borrow APY in RAY format. To compute deposit APY you should multiply it to utilisation rates&#x20;
* [getDieselRate\_RAY](../contracts/pools/pool-service.md#getborrowrate\_ray) provides current [diesel rate](../contracts/pools/economy.md#diesel-rate) in RAY format

## Risk takers side (Traders, farmers)

To manage virtual account, use specific smartcontract. Basic operation is implemented in AbstractVirtualManager contract, and it's common for all VAMs. You can use [IVirtualAccountManager](https://github.com/Gearbox-protocol/gearbox-v2/blob/master/contracts/interfaces/IVirtualAccountManager.sol) to check program interface. State-change methods which are common:

* [openVirtualAccount](../contracts/credit/credit\_manager.md#openvirtualaccount) which opens virtual account and reverts if user it already has. Check, the pool parameter virtualAccountManagersCanBorrow to be ensured that this VAM can borrow money before, and there is enough liquidity
* [closeVirtualAccount ](../contracts/credit/credit\_manager.md#closevirtualaccount)which converts all assets to underlying one, repays debt and return remaining funds to user. Check, that you can close account if it accrue losses to pool.
* [liquidateVirtualAccount](../contracts/credit/credit\_manager.md#liquidatevirtualaccount) liquidates account if it's Hf<1.
* [increaseBorrowedAmount](../contracts/credit/credit\_manager.md#increaseborrowedamount) increase borrowing amount to maximize.

### Getters:

* [hasOpenedVirtualAccount](../contracts/credit/credit\_manager.md#hasopenedvirtualaccount) checks that user has opened virtual account
* [calcVirtualAccountTotalValue](../contracts/credit/credit\_manager.md#calcvirtualaccounttotalvalue) computes [TotalValue](../contracts/credit/economy.md#total-value)
* [getVirtualAccountTokenBalance](../contracts/credit/credit\_manager.md#getvirtualaccounttokenbalance) return balance is i-asset for virtual account
* [calcVirtualAccountAccruedInterested ](../contracts/credit/credit\_manager.md#calcvirtualaccountaccruedinterested)calculates borrowedAmount + interest rate accrued
* [calcVirtualAccountHealthFactor](../contracts/credit/credit\_manager.md#calcvirtualaccounthealthfactor) calculates Health factor for virtual account
* [calcRepayAmount calculates](../contracts/credit/credit\_manager.md#calcrepayamount) Repay / Liquidation amount
* [getVirtualAccountParameters](../contracts/credit/credit\_manager.md#getvirtualaccountparameters) Returns basic virtual account parameters
* [getVirtualAccountTokenById](../contracts/credit/credit\_manager.md#getvirtualaccounttokenbyid) Returns token address + balance by token id&#x20;
* [allowedTokensCount](../contracts/credit/credit\_manager.md#allowedtokenscount) Returns allowed tokens count
* [allowedTokens](../contracts/credit/credit\_manager.md#allowedtokens) Returns allowed token address by its id
* [allowedContractsCount ](../contracts/credit/credit\_manager.md#allowedcontractscount)Returns allowed contract address by its id
* [allowedContracts](../contracts/credit/credit\_manager.md#allowedcontracts) Returns allowed contract address by its id

### Virtual account manager type

For specific functions which interacts with 3rd-party contracts (like DEXes, farms and so on), you should use functions which extendes abstract contract.

Use kind() method to get contract type:

| Return value | Contract                                        |
| ------------ | ----------------------------------------------- |
| trade        | [TraderVirtualAccountManager](broken-reference) |
| stable       | [StableVirtualAccountManager](broken-reference) |

### WETH Gateway

To interact with ETH and wrap into WETH, you should use WETHGateway contracе [IWETHGateway](https://github.com/Gearbox-protocol/gearbox-v2/blob/master/contracts/interfaces/IWETHGateway.sol) interface. WETHGateway can be got using [addressProvider](../contracts/core/addressprovider.md#getwethgateway).

* [addLiquidityETH](../contracts/core/wethgateway.md#addliquidityeth) wraps ETH to WETH and adds WETH liquidity to selected pool
* [removeLiquidityETH](../contracts/core/wethgateway.md#removeliquidityeth) withdraw liquidity from pool and convert WETH back to ETH
* [openVirtualAccountETH](../contracts/core/wethgateway.md#openvirtualaccounteth) Opens virtual account in ETH
* [repayVirtualAccountETH](../contracts/core/wethgateway.md#repayvirtualaccounteth) Repays virtual account

## Liquidations

### Getting all accounts

To get account which can be liquidated, you should check all possible accounts. At first, you should get [addressProvider](../contracts/core/addressprovider.md) and then get account factory. Now, there is only one virtual account factory - [TraderAccountFactory](broken-reference).

On the next step, you can iterate via all accounts using calling [virtualAccounts](../contracts/core/account-factory.md#virtualaccounts). You can get get the total quantity of accounts by calling [countVirtualAccounts](../contracts/core/account-factory.md#countvirtualaccounts).

After that you can subscribe to event NewVirtualAccount and update the list of all accounts in your bot. It's not necessary but highly recommened to check which accounts are in use.

### Subscribe to VAM events

As a bot, you should subscrive to 3 types of event of all interested virtual accounts managers:&#x20;

* [OpenVirtualAccount](../contracts/credit/credit\_manager.md#event\_openvirtualaccount)
* [CloseVirtualAccount](../contracts/credit/credit\_manager.md#event\_closevirtualaccount)
* [LiquidateVirtualAccount](../contracts/credit/credit\_manager.md#event\_liquidatevirtualaccount)
* [RepayVirtualAccount](../contracts/credit/credit\_manager.md#event\_repayvirtualaccount)

Based on this date, you can evluate all open account. And then in cycle, check all accounts using IVirtualAccountManager and call [hasOpenedVirtualAccount](../contracts/credit/credit\_manager.md#hasopenedvirtualaccount) to prove that this virtual account is still connected to manager and[ calcVirtualAccountHeathFactor](../contracts/credit/credit\_manager.md#calcvirtualaccounthealthfactor) to get current health factor.

If returned value is less than PERCENTAGE\_FACTOR (10000), it means that this account could be liquidated.

To liquiate account you have to call [liquidateVirtualAccount](../contracts/credit/credit\_manager.md#liquidatevirtualaccount).
