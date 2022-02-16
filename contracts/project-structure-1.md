# Protocol structure

## Folders

| Folder    | Description                                  |
| --------- | -------------------------------------------- |
| contracts | Contracts                                    |
| core      | Core models for typescript tests & models    |
| deployer  | Deployer & test suites scripts in typescript |
| docs      | Picrtures for documentaion                   |
| model     | Smart contract models in typescript          |
| scripts   | Deployment & other service scripts           |
| test      | Unit- & integrational tests                  |
| types     | Typechain types                              |
| utils     | Utils for typescripts types                  |

## Contracts

| Contract                    | Description                                                                                                                                                                                                                            |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **adapters/**               |                                                                                                                                                                                                                                        |
| Curve1.sol                  | Adapter for Curve V1                                                                                                                                                                                                                   |
| UniswapV2.sol               | Adapter for Uniswap V2 swaps type only (works with Sushiswap, pancake and other uniswap V2 comfortable systems)                                                                                                                        |
| UniswapV3.sol               | Adapter for Uniswap V3 swaps only.                                                                                                                                                                                                     |
| Yearn.sol                   | Adapter for Yearn V2 vaults.                                                                                                                                                                                                           |
| **core/**                   |                                                                                                                                                                                                                                        |
| AccountFactory.sol          | Account factory. Keeps all credit accounts, deploy them if needed                                                                                                                                                                      |
| AccountMining.sol           | <p>Account Mining contract, based on <a href="https://github.com/Uniswap/merkle-distributor">https://github.com/Uniswap/merkle-distributor</a></p><p>It's needed only during Account Mining phase before protocol will be launched</p> |
| ACL.sol                     | Keeps permissions for the system                                                                                                                                                                                                       |
| ACLTrait.sol                | Trait (Mixin) to extend contract to work with global permissions (ACL)                                                                                                                                                                 |
| AddressProvider.sol         | Contract which keeps all addresses of deployed core contracts                                                                                                                                                                          |
| ContractsRegister.sol       | Contract which keeps all addresses of pools & creditManagers                                                                                                                                                                           |
| DataCompressor.sol          | DataCompressor gets information from different contract and returns it to frontend. It reduces quantity of interaction between frontend (liquidation bots, etc.) and blockchain nodes.                                                 |
| WETHGateway.sol             | Helper to work with ETH. It converts Eth to Weth and vice versa                                                                                                                                                                        |
| **credit/**                 |                                                                                                                                                                                                                                        |
| CreditAccount.sol           | CreditAccount isolated smartcontract. DeFi primitive.                                                                                                                                                                                  |
| CreditFilter.sol            | Helper for CreditManager which keeps allowed token and contract lists, computes fast check, etc.                                                                                                                                       |
| CreditManagers.col          | Core credit contract, which makes core credit account opreations: open / close/ repay / increase borrow amount, etc.                                                                                                                   |
| LeverageActions.sol         | Autmoation actions for opening leveraged accounts                                                                                                                                                                                      |
| **integrations/**           |                                                                                                                                                                                                                                        |
| yearn/YearnPriceFeed.sol    | Price feed for yearn integration                                                                                                                                                                                                       |
| **libraries/**              |                                                                                                                                                                                                                                        |
| data/Types.sol              | Keeps all data structues used in system                                                                                                                                                                                                |
| helpers/Constants.sol       | Keeps all constants in the system                                                                                                                                                                                                      |
| helpers/Errors.sol          | Keeps all constants for error messages. Gearbox use 2-3 letter error message to reduce contract size. You can find explanation here.                                                                                                   |
| math/PercentageMath.sol     | Percentage math                                                                                                                                                                                                                        |
| WadRayMath.sol              | WadRay math, taken from [https://github.com/aave/aave-protocol/blob/master/contracts/libraries/WadRayMath.sol](https://github.com/aave/aave-protocol/blob/master/contracts/libraries/WadRayMath.sol)                                   |
| **oracles/**                |                                                                                                                                                                                                                                        |
| PriceOracle.sol             | Price oracle. Takes price data from Chainling price oracles and convert assets using these rates.                                                                                                                                      |
| **pool/**                   |                                                                                                                                                                                                                                        |
| LinearInterestRateModel.sol | Linear interest rate model implementation. Gearbox uses Aave Risk Framework: [https://docs.aave.com/risk/](https://docs.aave.com/risk/)                                                                                                |
| PoolService.sol             | Pool contract, it adds/removes liquidaty and alse lend credit managers                                                                                                                                                                 |
| **tokens/**                 |                                                                                                                                                                                                                                        |
| DieselToken.sol             | DieselToken - LP token for pools                                                                                                                                                                                                       |
| GearNFT.sol                 | Experimental NFT token for Gearbox contributors                                                                                                                                                                                        |
| GearToken.sol               | Governance Gearbox token based on [https://github.com/Uniswap/governance/blob/master/contracts/Uni.sol](https://github.com/Uniswap/governance/blob/master/contracts/Uni.sol)                                                           |
