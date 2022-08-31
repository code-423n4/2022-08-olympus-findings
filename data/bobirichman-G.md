# GAS REPORT

## [GAS] Remove casting of latestAnswer()
latestAnswer() naturally returns int and not uint. You can work with signed integers instead to save gas of the casting.

### Proof of concept:
- [PRICE.t.sol#L81](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol#L81)
- [PRICE.t.sol#L255](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol#L255)

## [GAS] Use assembly opcodes iszero in the following locations


### Proof of concept:
- [Governance.sol#L297](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L297)
- [PRICE.sol#L267](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L267)

## [GAS] Cache array size
You can cache the array size to improve gas usage in the following locations

Example: [Governance.sol#L277](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L277)

## [GAS] Use > instead != to compare uint with 0


### Proof of concept:
- [TRSRY.t.sol#L97](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/TRSRY.t.sol#L97)
- [TRSRY.t.sol#L107](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/TRSRY.t.sol#L107)

## [GAS] Mark as payable If has onlyOwner modifier
In order to save gas you can put a payable modifier for functions that are called by protocol owners.

### Proof of concept:
- [Governance.sol#L76](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L76)
- [TreasuryCustodian.sol#L75](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L75)
