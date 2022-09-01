# Overview
| Risk Rating | Number of issues |
| -------- | -------- |
| Low Risk     | 3     |
| Non-Critical     | 1     |


# Findings
## [L-01] Make `revokePolicyApprovals` stricter
[TreasuryCustodian.sol#L54](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L54)

```solidity
if (Policy(policy_).isActive()) revert PolicyStillActive();
```
As `revokePolicyApprovals` can be called by anyone, it is possible to revoke a non-policy contract that was given a treasury approval, if the contract has a public `isActive` function that returns `false` value.

Consider making the address validation stricter by also checking whether the address has a public `kernel` function which returns the same address as `TreasuryCustodian`'s kernel.

## [L-02] Missing zero address check in `KernelAdapter` constructor
[Kernel.sol#L65-L67](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L65-L67)

```solidity
constructor(Kernel kernel_) {
    kernel = kernel_;
}
```
A faulty deployment script might deploy a module/policy with zero address which would render the contract useless, incurring a gas cost for contract the re-deployment. 

Consider adding a zero address check for `kernel`.

## [L-03] Missing validation for `cushionFactor`
[Operator.sol#L134](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L134)
In `Operator.constructor()`, there is no check to make sure that `cushionFactor`/`configParams[0]` is within acceptable range (`100` to `10000`). A faulty deployment script might set a wrong value that could cause irregular behaviour during bond market creations.

Consider adding a check in `constructor()` to make sure the value is within acceptable range:
```
if (configParams[0] > 10000 || configParams[0] < 100) revert Operator_InvalidParams();
```

## [N-01] Comment Typo
[PRICE.sol#L126](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L126)

```solidity
// Cache numbe of observations to save gas.
```
should be:

```solidity
// Cache number of observations to save gas.
```