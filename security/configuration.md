# Configuration

### DAO & timelock

All configuration set can be done only with Configuration role (except ContractsRegister). This role will be transferred to DAO and community will decide how to set hte best protocol parameters. \
\
At this time, configurator can set up following parameters:

### PoolService

| Action                      | Description                                                                                                                                                                             |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| connectCreditManager        | Connects new credit manager to pool and allow to borrow & repay                                                                                                                         |
| forbidCreditManagerToBorrow | Forbid particular creditManager to borrow money. however it still return cab reoay funds back to pool, but cant lend                                                                    |
| newInterestRateModel        | set the new interest rate model. By design, model parameters are immutable, to change them, users should deploy new contract follwing IInterestRateModel, and then connect it with pool |
| setExpectedLiquidityLimit   | Sets the maximum amount for expected liquidity                                                                                                                                          |
| setWithdrawFee              | Set withdraw fee                                                                                                                                                                        |

### Credit manager

| Action    | Description                                                                                                          |
| --------- | -------------------------------------------------------------------------------------------------------------------- |
| setLimits | Sets minimal & maximum amount for one credit account                                                                 |
| setFees   | Set maxLeverage, success, interest and liquidataion fees and laso liquiation discount (= 100% - liquidation premium) |

### CreditFilter

| Action                 | Description                                                                                                                                           |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| allowToken             | Allow new token / update liquidation threshold                                                                                                        |
| allowContract          | Allow new contract / update contract adapter                                                                                                          |
| forbidContract         | Forbid contract and remove allowed adapter                                                                                                            |
| changeAllowedTokenMask | Enable/disable token in allowedTokenMask. If token was disabled (its' bit =0), user can't comvert money to it if previously credit account not use it |
