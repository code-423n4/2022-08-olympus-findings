# gas optimization

### Don't Initialize Variables with Default Value

### examples
```
2022-08-olympus\src\Kernel.sol::397 => for (uint256 i = 0; i < reqLength; ) {
2022-08-olympus\src\utils\KernelUtils.sol::43 => for (uint256 i = 0; i < 5; ) {
2022-08-olympus\src\utils\KernelUtils.sol::58 => for (uint256 i = 0; i < 32; ) {
```

### Use != 0 instead of > 0 for Unsigned Integer Comparison

### examples
```
2022-08-olympus\src\policies\Governance.sol::247 => if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
2022-08-olympus\src\utils\KernelUtils.sol::46 => if (char < 0x41 || char > 0x5A) revert InvalidKeycode(keycode_); // A-Z only
2022-08-olympus\src\utils\KernelUtils.sol::60 => if ((char < 0x61 || char > 0x7A) && char != 0x5f && char != 0x00) {
```

### Long Revert Strings

### examples
```
2022-08-olympus\src\modules\PRICE.sol::4 => import {AggregatorV2V3Interface} from "interfaces/AggregatorV2V3Interface.sol";
2022-08-olympus\src\modules\TRSRY.sol::5 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\modules\VOTES.sol::18 => ERC20("OlympusDAO Dummy Voting Tokens", "VOTES", 0)
2022-08-olympus\src\policies\BondCallback.sol::5 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\policies\Heart.sol::4 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\policies\Heart.sol::7 => import {IOperator} from "policies/interfaces/IOperator.sol";
2022-08-olympus\src\policies\Operator.sol::4 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\policies\Operator.sol::7 => import {IOperator} from "policies/interfaces/IOperator.sol";
```

### Use Shift Right/Left instead of Division/Multiplication if possible

### examples
```
2022-08-olympus\src\interfaces\IBondAuctioneer.sol::41 => /// @dev                        Should be calculated as: (payoutDecimals - quoteDecimals) - ((payoutPriceDecimals - quotePriceDecimals) / 2)
2022-08-olympus\src\policies\Operator.sol::372 => int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
2022-08-olympus\src\policies\Operator.sol::419 => uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
2022-08-olympus\src\policies\Operator.sol::420 => uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
2022-08-olympus\src\policies\Operator.sol::427 => int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
2022-08-olympus\src\policies\Operator.sol::786 => ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
```
