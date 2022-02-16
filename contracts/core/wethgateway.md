# WETHGateway

ETH <=> WETH wrapper for Gearbox protocol. It implements [IWETHGateway](https://github.com/Gearbox-protocol/gearbox-v2/blob/master/contracts/interfaces/IWETHGateway.sol) interface.

## Code

[WETHGateway.sol](https://github.com/Gearbox-protocol/gearbox-contracts/blob/master/contracts/core/WETHGateway.sol)

## State-Changing Functions

### addLiquidityETH

```
function addLiquidityETH(
    address pool,
    address onBehalfOf,
    uint16 referralCode
)
    external
```

| Parameter    | Description                                                                                                                                                                                               |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| pool         | Address of PoolService contract which where user wants to add liquidity. This pool should has WETH as underlying asset                                                                                    |
| onBehalfOf   | The address that will receive the diesel tokens, same as msg.sender if the user wants to receive them on his own wallet, or a different address if the beneficiary of diesel tokens is a different wallet |
| referralCode | Code used to register the integrator originating the operation, for potential rewards. Set "0" if the action is executed directly by the user, without any middle-man                                     |

Convert ETH to WETH and [add liqudity](../pools/pool-service.md#addliquidity) to pool.

### removeLiquidityETH

```
function removeLiquidityETH(
    address pool,
    uint256 amount,
    address payable to
)
external
```

| Parameters | Description                                                                                                                 |
| ---------- | --------------------------------------------------------------------------------------------------------------------------- |
| pool       | Address of PoolService contract which where user wants to withdraw liquidity. This pool should has WETH as underlying asset |
| amount     | Amount of tokens to be transfer                                                                                             |
| to         | Address to transfer liquidity                                                                                               |

Removes liquidity from pool and convert WETH back to ETH

### openVirtualAccountETH

```
function openVirtualAccountETH(
    address vam,
    address payable onBehalfOf,
    uint256 leverageFactor,
    uint256 referralCode
)
external
payable
```

| Parameters     | Descritpion                                                                                                                                                                   |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| vam            | Address of virtual account manager. Should used WETH as underlying asset                                                                                                      |
| onBehalfOf     | The address that we open virtual account. Same as msg.sender if the user wants to open it for his own wallet, or a different address if the beneficiary is a different wallet |
| leverageFactor | Multiplier to borrowers own funds                                                                                                                                             |
| referralCode   | Code used to register the integrator originating the operation, for potential rewards. Set "0" if the action is executed directly by the user, without any middle-man         |

Opens virtual account in ETH

### repayVirtualAccountETH

```
function repayVirtualAccountETH(address vam, address to)
external
payable
```

| Parameters | Description                                                              |
| ---------- | ------------------------------------------------------------------------ |
| vam        | Address of virtual account manager. Should used WETH as underlying asset |
| to         | Address to send virtual account assets                                   |
