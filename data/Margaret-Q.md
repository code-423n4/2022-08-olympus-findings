
# QA REPORT

## [QA 00] Consider adding to the following contracts an MIT license


### Proof of concept:
- [Operator.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol)
- [TreasuryCustodian.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/TreasuryCustodian.t.sol)
- [PriceConfig.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol)
- [BondCallback.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol)
- [Deploy.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/scripts/Deploy.sol)

## [QA 01] Add Timelock for the following functions:


### Proof of concept:
- [VoterRegistration.t.sol#L24](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/VoterRegistration.t.sol#L24)
- [TRSRY.sol#L126](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L126)
- [Governance.t.sol#L46](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L46)
- [RANGE.t.sol#L35](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L35)
- [TreasuryCustodian.t.sol#L26](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/TreasuryCustodian.t.sol#L26)
- [RANGE.sol#L242](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L242)
- [Operator.sol#L563](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L563)
- [PRICE.t.sol#L33](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol#L33)
- [RANGE.sol#L263](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L263)
