# Gas Optimizations
* Use the `calldata` modifier instead of `memory` for array, struct and dynamic arguments in order to save gas.
  * `PRICE` - line 205
  * `RANGE` - lines 79-80
  * `BondCallback` - line 152
  * `Governance` - line 162
  * `Operator` - line 96-97
  * `PriceConfig` - line 45
  * `TreasuryCustodian` - line 53
* Unroll the loop in `KernelUtils.ensureValidKeycode()` to save gas spent on loop index and condition operations
* Line 452 in the `Kernel` contract (`if (!isRole[role_]) revert Kernel_RoleDoesNotExist(role_);`) is redundant because `!isRole[role_] => !hasRole[addr_][role_]`
* In the `Kernel._activatePolicy()` function, in order to avoid a redundant calculation, use:
    ```sol
    activePolicies.push(policy_);
    getPolicyIndex[policy_] = activePolicies.length - 1;
    ```
    instead of
    ```sol
    getPolicyIndex[policy_] = activePolicies.length;
    activePolicies.push(policy_);
    ``` 
    (This optimization can be done in more places)
* Cache `activeProposal.proposalId` in the `Governance.vote()` function
* Cache `updateMovingAverage` in the `PRICE.updateMovingAverage()`
* Prefix incrementation and decrementation costs less gas than postfix incrementation and decrementation.
  * Operator - lines 488, 670, 675, 686, 691
  * KernelUtils - lines 49, 64
* Initializing variables to their default value is done automatically, and doing it manually actually costs more gas.
  * Kernel - line 397
  * KernelUtils - lines 43, 58
* Cache the array length outside of the loop to avoid accessing it in every iteration:
  * Governance - line 278
* Use `!= 0` instead of `> 0` when comparing a `uint` with 0:
    OlympusERC20 - line 611
    FullMath - lines 35, 122
    Governance - line 247
* Strings are broken into 32 byte chunks for operations. Revert error strings over 32 bytes therefore consume extra gas than shorter strings, as documented publicly. Reducing revert error strings to under 32 bytes decreases deployment time gas and runtime gas when the revert condition is met. Alternatively, use custom errors, introduced in Solidity 0.8.4: https://blog.soliditylang.org/2021/04/21/custom-errors
* Use left/right shift instead of multiplying/dividing by powers of 2 (x \* (2\*\*i) == x << i, x / (2\*\*i) == x >> i)
  * Operator - lines 372, 419-420, 427, 786
