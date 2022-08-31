# Gas optmization report

## [G-01] Set to `payable` permissionened functions that are guaranteed to revert for normal users
It will skip test that will lower the deployment and usage costs.
The extra opcodes avoided are `CALLVALUE(2)`, `DUP1(3)`, `ISZERO(3)`, `PUSH2(3)`, `JUMPI(10)`, `PUSH1(3)`, `DUP1(3)`, `REVERT(0)`, `JUMPDEST(1)`, `POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

- [BondCallback.sol#152](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#152)
- [BondCallback.sol#190](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#190)

Heart.sol
- [Heart.sol#132](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#132)
- [Heart.sol#135](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#135)
- [Heart.sol#142](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#142)
- [Heart.sol#150](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#150)

For this change to work, you need to set to `payable` the following function in the interface IHeart.sol respectively:

IHeart.sol:
- [IHeart.sol#24](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#25)
- [IHeart.sol#28](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#28)
- [IHeart.sol#34](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#34)
- [IHeart.sol#38](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#38)

Operator.sol
- [Operator.sol#195](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#195)
- [Operator.sol#349](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#349)
- [Operator.sol#500](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#500)
- [Operator.sol#510](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#510)
- [Operator.sol#516](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#516)
- [Operator.sol#531](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#531)
- [Operator.sol#548](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#548)
- [Operator.sol#563](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#563)
- [Operator.sol#588](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#588)
- [Operator.sol#598](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#598)
- [Operator.sol#618](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#618)
- [Operator.sol#624](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#624)

For this change to work, you need to set to `payable` the following function in the interface IHeart.sol respectively:

IOperator.sol:
- [IOperator.sol#42](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#42)
- [IOperator.sol#74](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#74)
- [IOperator.sol#80](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#80)
- [IOperator.sol#85](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#85)
- [IOperator.sol#96](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#96)
- [IOperator.sol#101](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#101)
- [IOperator.sol#113](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#113)
- [IOperator.sol#119](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#119)
- [IOperator.sol#125](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#125)
- [IOperator.sol#131](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#131)
- [IOperator.sol#136](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#136)

PriceConfig.sol:
- [PriceConfig.sol#46](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#46)
- [PriceConfig.sol#60](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#60)
- [PriceConfig.sol#70](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#70)

TreasuryCustodian.sol:
- [TreasuryCustodian.sol#42](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#42)
- [TreasuryCustodian.sol#75](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#75)
- [TreasuryCustodian.sol#84](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#84)

VoterRegistration.sol:
- [VoterRegistration.sol#45](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#45)
- [VoterRegistration.sol#53](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#53)

And the same goes for the following modifiers:
- onlyOwner
- onlyExecutor
- onlyAdmin
- onlyGovernor
- onlyGuardian
- onlyPermitted
- onlyVault

## [G-02] Usage of boolean state variable in storage

Use `uint256(1)` and `uint256(2)` for true/false to avoid a `Gwarmaccess` (100 gas) for the extra `SLOAD`, and to avoid `Gsset` (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.
See more [here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)

- [Heart.sol#33](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#33)
- [Operator.sol#62](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#62)
- [Operator.sol#66](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#66)
- [Kernel.sol#113](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#113)
- [BondCallback.sol#24](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#24)
- [Governance.sol#105](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#105)
- [Governance.sol#117](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#117)

## [G-03] Do not initialize non constant/non immutable vairable to zero
Not overwriting the default value for stack variable saves 8 gas per loop.

[Kernel.sol#397](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#397)

