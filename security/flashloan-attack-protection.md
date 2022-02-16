# Flashloan attack protection

### Attack description

The gearbox protocol could be used as a "free" multiplier for a flash loan to manipulate rates on swap protocol. &#x20;

Let's assume, that attacker needs $100M to manipulate the rate.&#x20;

There are 2 different options:

Option 1: Take $100M from Aave and pay $100K for the loan

Option 2:

1. Take $1M from Aave for $1K commission
2. Open leverage position in Gearbox x100 for stable pool
3. Manipulate rate
4. Close position and pay 1% for $1M, which is $10K
5. Repay flash loan

As you can see, the second option is 10 times cheaper.

### Solution

The gearbox protocol forbid to open & close the virtual account in one block.&#x20;

### Test

[FlashLoanAttacker.sol](https://github.com/Gearbox-protocol/gearbox-v2/blob/master/contracts/mocks/attackers/FlashLoanAttacker.sol)
