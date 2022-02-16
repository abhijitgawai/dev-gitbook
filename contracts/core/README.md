# Core

### Core

Core contracts are services which serves pools and virtual account managers

![](../../.gitbook/assets/Gearbox\_white\_high.011.png)

Gearbox core is a service layer that provides the following services:

| Contract                               | Service                                                              |
| -------------------------------------- | -------------------------------------------------------------------- |
| [Address provider](addressprovider.md) | Keeps addresses of core contracts. Used for smart contract discovery |
| [Account Factory](account-factory.md)  | Supply reusable credit accounts                                      |
| [ContractsRegister](poolregistry.md)   | Keeps pool registry, used for pool contracts discovery               |
| [ACL](acl.md)                          | Keeps admins roles                                                   |
| [Price oracle](../priceoracle.md)      | Provides assets prices for risk model                                |
| WETHGateway                            | Gateway to convert ETH to WETH and vice versa                        |
