# GAS REPORT

## [GAS 00] Declare as payable If the onlyOwner modifier is present
You can save gas by adding a payable modifier to functions that can be only called by protocol owners.

### Proof of concept:
- [Operator.sol#L563](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L563)
- [TreasuryCustodian.sol#L84](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L84)
- [Operator.sol#L624](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L624)
- [PriceConfig.sol#L61](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L61)
- [BondCallback.sol#L87](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L87)
- [Operator.sol#L350](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L350)
- [VoterRegistration.sol#L53](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/VoterRegistration.sol#L53)
- [VoterRegistration.sol#L45](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/VoterRegistration.sol#L45)
- [BondCallback.sol#L190](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L190)
- [PriceConfig.sol#L72](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L72)
- [BondCallback.sol#L67](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L67)
- [TreasuryCustodian.sol#L46](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L46)
- [Kernel.sol#L235](https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L235)
