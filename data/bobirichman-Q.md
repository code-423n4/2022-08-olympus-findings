
# QA REPORT

## [LOW] Approve 0 first
At some tokens you can approve an amount (at USDT for instance) only after approving to 0. Consider using increase/decrease approve notation instead.

### Proof of concept:
- [BondCallback.t.sol#L476](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L476)
- [Governance.t.sol#L101](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L101)

## [LOW] Use safeApprove
Use safeApprove in the following locations

### Proof of concept:
- [BondCallback.t.sol#L208](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L208)
- [BondCallback.t.sol#L211](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L211)

## [LOW] Use mult before div
To improve the following calculations precision consider changing the order of the operations such that multiplications come before divisions.

Example: [BondCallback.t.sol#L270](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L270)

## [LOW] Missing pause functionality


### Proof of concept:
- [Deploy.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/scripts/Deploy.sol)
- [Governance.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol)

## [LOW] Consider replacing assert with require
Assertions are a bad practice, use require instead.

### Proof of concept:
- [BondCallback.t.sol#L476](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L476)
- [BondCallback.t.sol#L483](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L483)

## [LOW] Not verified input
At the following functions you should verify the parameters that are being assigned to a state variable.

### Proof of concept:
- [Kernel.sol#L235](https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L235)
- [MINTR.t.sol#L117](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/MINTR.t.sol#L117)

## [LOW] Missing nonReentrancy modifier
The following functions allows attackers to try reentrancy since they are calling to external contracts / transferring eth. Consider adding a nonReentrancy modifier.

### Proof of concept:
- [VOTES.t.sol#L46](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/VOTES.t.sol#L46)
- [PRICE.t.sol#L382](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol#L382)

## [NON CRITICAL] Missing function spec comments


### Proof of concept:
- [PRICE.t.sol#L323](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol#L323)
- [VOTES.sol#L39](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L39)

## [NON CRITICAL] Consider emitting an event at the following functions


### Proof of concept:
- [Kernel.sol#L217](https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L217)
- [Kernel.t.sol#L38](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L38)

## [NON CRITICAL] The following events are not indexed


### Proof of concept:
- [Operator.sol#L53](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L53)
- [Heart.sol#L30](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L30)

## [NON CRITICAL] NonReentrancy should be the first modifier in order


Example: [Operator.sol#L276](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L276)

## [NON CRITICAL] Floating pragma
Floating pragma is a bad practice, since it does not guaranty the same version at future deployments.

### Proof of concept:
- [TreasuryCustodian.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/TreasuryCustodian.t.sol)
- [PriceConfig.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/PriceConfig.t.sol)
