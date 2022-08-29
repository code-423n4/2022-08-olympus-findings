### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
src/policies/Governance.sol::259 => VOTES.transferFrom(msg.sender, address(this), userVotes);
src/policies/Governance.sol::312 => VOTES.transferFrom(address(this), msg.sender, userVotes);
src/test/lib/bonds/BondFixedTermTeller.sol::115 => underlying_.transferFrom(msg.sender, address(this), amount_);
src/test/modules/MINTR.t.sol::87 => ohm.approve(address(MINTR), amount_);
src/test/modules/MINTR.t.sol::107 => ohm.approve(users[0], amount_);
src/test/modules/TRSRY.t.sol::156 => ngmi.approve(address(TRSRY), amount_);
src/test/modules/VOTES.t.sol::50 => VOTES.transfer(address(0), 10);
src/test/modules/VOTES.t.sol::74 => VOTES.transferFrom(address(1), address(2), 3);
src/test/policies/BondCallback.t.sol::207 => ohm.approve(address(operator), testOhm * 20);
src/test/policies/BondCallback.t.sol::209 => reserve.approve(address(operator), testReserve * 20);
src/test/policies/BondCallback.t.sol::212 => ohm.approve(address(teller), testOhm * 20);
src/test/policies/BondCallback.t.sol::214 => reserve.approve(address(teller), testReserve * 20);
src/test/policies/Governance.t.sol::96 => VOTES.approve(address(governance), type(uint256).max);
src/test/policies/Governance.t.sol::98 => VOTES.approve(address(governance), type(uint256).max);
src/test/policies/Governance.t.sol::100 => VOTES.approve(address(governance), type(uint256).max);
src/test/policies/Governance.t.sol::102 => VOTES.approve(address(governance), type(uint256).max);
src/test/policies/Governance.t.sol::104 => VOTES.approve(address(governance), type(uint256).max);
src/test/policies/Operator.t.sol::192 => ohm.approve(address(operator), testOhm * 20);
src/test/policies/Operator.t.sol::194 => reserve.approve(address(operator), testReserve * 20);
src/test/policies/Operator.t.sol::197 => ohm.approve(address(teller), testOhm * 20);
src/test/policies/Operator.t.sol::199 => reserve.approve(address(teller), testReserve * 20);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
src/external/OlympusERC20.sol::2 => pragma solidity >=0.7.5;
src/interfaces/AggregatorV2V3Interface.sol::2 => pragma solidity ^0.8.0;
src/interfaces/IBondAggregator.sol::2 => pragma solidity >=0.8.0;
src/interfaces/IBondAuctioneer.sol::2 => pragma solidity >=0.8.0;
src/interfaces/IBondCallback.sol::2 => pragma solidity >=0.8.0;
src/interfaces/IBondTeller.sol::2 => pragma solidity >=0.8.0;
src/interfaces/IWETH9.sol::2 => pragma solidity >=0.8.0;
src/libraries/FullMath.sol::2 => pragma solidity >=0.8.0;
src/libraries/TransferHelper.sol::2 => pragma solidity >=0.8.0;
src/policies/interfaces/IHeart.sol::2 => pragma solidity >=0.8.0;
src/policies/interfaces/IOperator.sol::2 => pragma solidity >=0.8.0;
src/test/lib/ModuleTestFixtureGenerator.sol::2 => pragma solidity >=0.8.0;
src/test/lib/UserFactory.sol::2 => pragma solidity >=0.8.0;
src/test/lib/bonds/interfaces/IBondAggregator.sol::2 => pragma solidity >=0.8.0;
src/test/lib/bonds/interfaces/IBondAuctioneer.sol::2 => pragma solidity >=0.8.0;
src/test/lib/bonds/interfaces/IBondCDA.sol::2 => pragma solidity >=0.8.0;
src/test/lib/bonds/interfaces/IBondCallback.sol::2 => pragma solidity >=0.8.0;
src/test/lib/bonds/interfaces/IBondFixedTermTeller.sol::2 => pragma solidity >=0.8.0;
src/test/lib/bonds/interfaces/IBondTeller.sol::2 => pragma solidity >=0.8.0;
src/test/lib/bonds/lib/ERC1155.sol::2 => pragma solidity >=0.8.0;
src/test/lib/larping.sol::1 => pragma solidity >=0.8.0;
src/test/mocks/MockPriceFeed.sol::2 => pragma solidity ^0.8.0;
src/test/modules/INSTR.t.sol::2 => pragma solidity >=0.8.0;
src/test/modules/PRICE.t.sol::2 => pragma solidity >=0.8.0;
src/test/modules/RANGE.t.sol::2 => pragma solidity >=0.8.0;
src/test/modules/VOTES.t.sol::3 => pragma solidity >=0.8.0;
src/test/policies/BondCallback.t.sol::2 => pragma solidity >=0.8.0;
src/test/policies/Governance.t.sol::2 => pragma solidity >=0.8.0;
src/test/policies/Heart.t.sol::2 => pragma solidity >=0.8.0;
src/test/policies/Operator.t.sol::2 => pragma solidity >=0.8.0;
src/test/policies/PriceConfig.t.sol::2 => pragma solidity >=0.8.0;
src/test/policies/TreasuryCustodian.t.sol::2 => pragma solidity >=0.8.0;
src/test/policies/VoterRegistration.t.sol::2 => pragma solidity >=0.8.0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Do not use Deprecated Library Functions

#### Impact
Issue Information: [L005](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l005---do-not-use-deprecated-library-functions)

#### Findings:
```
src/libraries/TransferHelper.sol::35 => function safeApprove(
src/policies/BondCallback.sol::57 => ohm.safeApprove(address(MINTR), type(uint256).max);
src/policies/Operator.sol::167 => ohm.safeApprove(address(MINTR), type(uint256).max);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

