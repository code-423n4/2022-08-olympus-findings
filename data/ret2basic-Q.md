# Olympus DAO Contest QA Report

## Summary

These QA issues were found during the code audit:

1. Unspecific Compiler Version Pragma (33 instances)
2. Do not use Deprecated Library Functions (3 instances)

Total 36 instances of 2 issues.

## 1. Unspecific Compiler Version Pragma (33 instances)

Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
2022-08-olympus/src/external/OlympusERC20.sol::2 => pragma solidity >=0.7.5;

2022-08-olympus/src/interfaces/AggregatorV2V3Interface.sol::2 => pragma solidity ^0.8.0;

2022-08-olympus/src/interfaces/IBondAggregator.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/interfaces/IBondAuctioneer.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/interfaces/IBondCallback.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/interfaces/IBondTeller.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/interfaces/IWETH9.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/libraries/FullMath.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/libraries/TransferHelper.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/policies/interfaces/IHeart.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/policies/interfaces/IOperator.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/lib/ModuleTestFixtureGenerator.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/lib/UserFactory.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAggregator.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAuctioneer.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondCDA.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondCallback.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondFixedTermTeller.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondTeller.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/lib/bonds/lib/ERC1155.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/lib/larping.sol::1 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::2 => pragma solidity ^0.8.0;

2022-08-olympus/src/test/modules/INSTR.t.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/modules/PRICE.t.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/modules/RANGE.t.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/modules/VOTES.t.sol::3 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/policies/BondCallback.t.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/policies/Governance.t.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/policies/Heart.t.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/policies/Operator.t.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/policies/PriceConfig.t.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/policies/TreasuryCustodian.t.sol::2 => pragma solidity >=0.8.0;

2022-08-olympus/src/test/policies/VoterRegistration.t.sol::2 => pragma solidity >=0.8.0;
```

## Do not use Deprecated Library Functions (3 instances)

The usage of deprecated library functions should be discouraged. This issue is mostly related to OpenZeppelin libraries.

```solidity
2022-08-olympus/src/libraries/TransferHelper.sol::35 => function safeApprove(

2022-08-olympus/src/policies/BondCallback.sol::57 => ohm.safeApprove(address(MINTR), type(uint256).max);

2022-08-olympus/src/policies/Operator.sol::167 => ohm.safeApprove(address(MINTR), type(uint256).max);
```
