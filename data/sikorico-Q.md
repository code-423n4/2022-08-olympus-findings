# QA REPORT

## [QA 00] Not verified input for important functions


### Proof of concept:
- [MINTR.t.sol#L117](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/MINTR.t.sol#L117)
- [Kernel.sol#L235](https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L235)

## [QA 01] Missing MIT licence for the following contracts


### Proof of concept:
- [TreasuryCustodian.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/TreasuryCustodian.t.sol)
- [Heart.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Heart.t.sol)
- [Deploy.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/scripts/Deploy.sol)
- [BondCallback.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol)
- [BondCallback.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol)

## [QA 02] Not emitted event for state changes


### Proof of concept:
- [Kernel.t.sol#L86](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L86)
- [Kernel.t.sol#L270](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L270)
- [Kernel.t.sol#L74](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L74)
- [VOTES.t.sol#L23](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/VOTES.t.sol#L23)
- [Governance.t.sol#L46](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L46)
- [Operator.sol#L624](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L624)
- [RANGE.t.sol#L453](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L453)
- [BondCallback.sol#L190](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L190)
- [VoterRegistration.t.sol#L24](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/VoterRegistration.t.sol#L24)
- [TRSRY.t.sol#L28](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/TRSRY.t.sol#L28)
- [Kernel.t.sol#L108](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L108)
- [Kernel.t.sol#L165](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L165)
- [Kernel.t.sol#L38](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L38)
- [PriceConfig.t.sol#L37](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/PriceConfig.t.sol#L37)
- [Heart.sol#L130](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L130)
- [MINTR.sol#L15](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L15)
- [Kernel.t.sol#L62](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L62)
- [VoterRegistration.sol#L19](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/VoterRegistration.sol#L19)
- [RANGE.t.sol#L424](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L424)

## [QA 03] Missing event indexers


### Proof of concept:
- [Operator.sol#L54](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L54)
- [Operator.sol#L51](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L51)
- [Governance.t.sol#L40](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L40)
- [RANGE.sol#L23](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L23)
- [Governance.t.sol#L41](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L41)
- [PRICE.sol#L28](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L28)
- [RANGE.sol#L20](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L20)
- [Governance.t.sol#L43](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L43)
- [Governance.t.sol#L39](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L39)
- [PRICE.sol#L27](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L27)
- [RANGE.t.sol#L202](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L202)
- [RANGE.sol#L30](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L30)
- [RANGE.sol#L22](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L22)

## [QA 04] Remove the name from the following unused function parameters


### Proof of concept:
- [BondCallback.sol#L42](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L42)
- [TreasuryCustodian.sol#L24](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L24)
- [PriceConfig.sol#L15](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L15)
- [MINTR.sol#L15](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L15)
- [PRICE.sol#L77](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L77)
- [VoterRegistration.sol#L16](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/VoterRegistration.sol#L16)
