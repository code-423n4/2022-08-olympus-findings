## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Function should revert if no valid action is found | 1 |
| [L&#x2011;02] | `safeApprove()` is deprecated | 2 |
| [L&#x2011;03] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 2 |
| [L&#x2011;04] | Open TODOs | 4 |

Total: 9 instances over 4 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Contracts will stop working when `block.timestamp` overflows `uint48` | 2 |
| [N&#x2011;02] | The `nonReentrant` `modifier` should occur before all other modifiers | 1 |
| [N&#x2011;03] | `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings | 2 |
| [N&#x2011;04] | `public` functions not called by the contract should be declared `external` instead | 9 |
| [N&#x2011;05] | Non-assembly method available | 1 |
| [N&#x2011;06] | `constant`s should be defined rather than using magic numbers | 65 |
| [N&#x2011;07] | Missing event and or timelock for critical parameter change | 3 |
| [N&#x2011;08] | Constant redefined elsewhere | 4 |
| [N&#x2011;09] | Lines are too long | 8 |
| [N&#x2011;10] | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 18 |
| [N&#x2011;11] | Typos | 4 |
| [N&#x2011;12] | File is missing NatSpec | 2 |
| [N&#x2011;13] | NatSpec is incomplete | 7 |
| [N&#x2011;14] | Event is missing `indexed` fields | 30 |
| [N&#x2011;15] | Not using the named return variables anywhere in the function is confusing | 14 |
| [N&#x2011;16] | Avoid the use of sensitive terms | 1 |

Total: 171 instances over 16 issues

## Low Risk Issues

### [L&#x2011;01]  Function should revert if no valid action is found
There is no final `else`, so `ActionExecuted` is incorrectly emitted if the action is unknown (incorrect state handling)

*There is 1 instance of this issue:*
```solidity
File: /src/Kernel.sol

254          } else if (action_ == Actions.MigrateKernel) {
255              ensureContract(target_);
256              _migrateKernel(Kernel(target_));
257          }
258  
259:         emit ActionExecuted(action_, target_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L254-L259

### [L&#x2011;02]  `safeApprove()` is deprecated
[Deprecated](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45) in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. If only setting the initial allowance to the value that means infinite, `safeIncreaseAllowance()` can be used instead. The function may currently work, but if a bug is found in this version of OpenZeppelin, and the version that you're forced to upgrade to no longer has this function, you'll encounter unnecessary delays in porting and testing replacement contracts.

*There are 2 instances of this issue:*
```solidity
File: src/policies/BondCallback.sol

57:           ohm.safeApprove(address(MINTR), type(uint256).max);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L57

```solidity
File: src/policies/Operator.sol

167:          ohm.safeApprove(address(MINTR), type(uint256).max);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L167

### [L&#x2011;03]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 2 instances of this issue:*
```solidity
File: src/Kernel.sol

251:              executor = target_;

253:              admin = target_;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L251

### [L&#x2011;04]  Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

*There are 4 instances of this issue:*
```solidity
File: src/policies/Operator.sol

657:          /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L657

```solidity
File: src/policies/TreasuryCustodian.sol

51:       // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.

52:       // TODO must reorg policy storage to be able to check for deactivated policies.

56:           // TODO Make sure `policy_` is an actual policy and not a random address.

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L51

## Non-critical Issues

### [N&#x2011;01]  Contracts will stop working when `block.timestamp` overflows `uint48`
This will be in ~9 million years. The DAO is supposed to live forever, right? RIGHT?!

*There are 2 instances of this issue:*
```solidity
File: /src/modules/PRICE.sol

143:         lastObservationTime = uint48(block.timestamp);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L143

```solidity
File: /src/policies/Operator.sol

210:             uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L210

### [N&#x2011;02]  The `nonReentrant` `modifier` should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

*There is 1 instance of this issue:*
```solidity
File: src/policies/Operator.sol

276:      ) external override onlyWhileActive nonReentrant returns (uint256 amountOut) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L276

### [N&#x2011;03]  `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings

*There are 2 instances of this issue:*
```solidity
File: src/modules/VOTES.sol

45:       function transfer(address to_, uint256 amount_) public pure override returns (bool) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L45

### [N&#x2011;04]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 9 instances of this issue:*
```solidity
File: src/Kernel.sol

439:      function grantRole(Role role_, address addr_) public onlyAdmin {

451:      function revokeRole(Role role_, address addr_) public onlyAdmin {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L439

```solidity
File: src/modules/INSTR.sol

37:       function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L37

```solidity
File: src/modules/MINTR.sol

33:       function mintOhm(address to_, uint256 amount_) public permissioned {

37:       function burnOhm(address from_, uint256 amount_) public permissioned {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/MINTR.sol#L33

```solidity
File: src/modules/RANGE.sol

215       function updateMarket(
216           bool high_,
217           uint256 market_,
218           uint256 marketCapacity_
219:      ) public permissioned {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L215-L219

```solidity
File: src/modules/TRSRY.sol

75        function withdrawReserves(
76            address to_,
77            ERC20 token_,
78:           uint256 amount_

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L75-L78

```solidity
File: src/policies/Governance.sol

145:      function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {

151:      function getActiveProposal() public view returns (ActivatedProposal memory) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L145

### [N&#x2011;05]  Non-assembly method available 
`assembly{ id := chainid() }` => `uint256 id = block.chainid`, `assembly { size := extcodesize() }` => `uint256 size = address().code.length`
There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary

*There is 1 instance of this issue:*
```solidity
File: src/utils/KernelUtils.sol

34:           size := extcodesize(target_)

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L34

### [N&#x2011;06]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 65 instances of this issue:*
```solidity
File: src/modules/PRICE.sol

/// @audit 38
90:           if (exponent > 38) revert Price_InvalidParams();

/// @audit 3
165:              if (updatedAt < block.timestamp - 3 * uint256(observationFrequency))

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L90

```solidity
File: src/modules/RANGE.sol

/// @audit 3
80:           uint256[3] memory rangeParams_ // [thresholdFactor, cushionSpread, wallSpread]

/// @audit 10000
245:              wallSpread_ > 10000 ||

/// @audit 100
246:              wallSpread_ < 100 ||

/// @audit 10000
247:              cushionSpread_ > 10000 ||

/// @audit 100
248:              cushionSpread_ < 100 ||

/// @audit 10000
/// @audit 100
264:          if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L80

```solidity
File: src/policies/BondCallback.sol

/// @audit 4
71:           requests = new Permissions[](4);

/// @audit 3
75:           requests[3] = Permissions(MINTR_KEYCODE, MINTR.burnOhm.selector);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L71

```solidity
File: src/policies/Governance.sol

/// @audit 10000
164:          if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)

/// @audit 100
217:              (totalEndorsementsForProposal[proposalId_] * 100) <

/// @audit 100
268:          if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L164

```solidity
File: src/policies/Operator.sol

/// @audit 8
97:           uint32[8] memory configParams // [cushionFactor, cushionDuration, cushionDebtBuffer, cushionDepositInterval, reserveFactor, regenWait, regenThreshold, regenObserve]

/// @audit 7
103:          if (configParams[1] > uint256(7 days) || configParams[1] < uint256(1 days))

/// @audit 10_000
106:          if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();

/// @audit 3
/// @audit 3
108:          if (configParams[3] < uint32(1 hours) || configParams[3] > configParams[1])

/// @audit 4
/// @audit 10000
/// @audit 4
/// @audit 100
111:          if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();

/// @audit 5
114:              configParams[5] < 1 hours ||

/// @audit 6
/// @audit 7
115:              configParams[6] > configParams[7] ||

/// @audit 7
116:              configParams[7] == uint32(0)

/// @audit 7
130:              observations: new bool[](configParams[7])

/// @audit 3
137:              cushionDepositInterval: configParams[3],

/// @audit 4
138:              reserveFactor: configParams[4],

/// @audit 5
139:              regenWait: configParams[5],

/// @audit 6
140:              regenThreshold: configParams[6],

/// @audit 7
141:              regenObserve: configParams[7]

/// @audit 3
147:          emit CushionParamsChanged(configParams[1], configParams[2], configParams[3]);

/// @audit 4
148:          emit ReserveFactorChanged(configParams[4]);

/// @audit 5
/// @audit 6
/// @audit 7
149:          emit RegenParamsChanged(configParams[5], configParams[6], configParams[7]);

/// @audit 4
155:          dependencies = new Keycode[](4);

/// @audit 3
159:          dependencies[3] = toKeycode("MINTR");

/// @audit 3
164:          MINTR = OlympusMinter(getModuleAddress(dependencies[3]));

/// @audit 9
176:          requests = new Permissions[](9);

/// @audit 3
180:          requests[3] = Permissions(RANGE_KEYCODE, RANGE.regenerate.selector);

/// @audit 4
181:          requests[4] = Permissions(RANGE_KEYCODE, RANGE.setSpreads.selector);

/// @audit 5
182:          requests[5] = Permissions(RANGE_KEYCODE, RANGE.setThresholdFactor.selector);

/// @audit 6
183:          requests[6] = Permissions(TRSRY_KEYCODE, TRSRY.setApprovalFor.selector);

/// @audit 7
184:          requests[7] = Permissions(MINTR_KEYCODE, MINTR.mintOhm.selector);

/// @audit 8
185:          requests[8] = Permissions(MINTR_KEYCODE, MINTR.burnOhm.selector);

/// @audit 36
378:                      36 + scaleAdjustment + int8(reserveDecimals) - int8(ohmDecimals) - priceDecimals

/// @audit 36
433:                      36 + scaleAdjustment + int8(ohmDecimals) - int8(reserveDecimals) - priceDecimals

/// @audit 10000
/// @audit 100
518:          if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();

/// @audit 7
533:          if (duration_ > uint256(7 days) || duration_ < uint256(1 days))

/// @audit 10_000
535:          if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();

/// @audit 10000
/// @audit 100
550:          if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L97

```solidity
File: src/policies/PriceConfig.sol

/// @audit 3
31:           permissions = new Permissions[](3);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L31

```solidity
File: src/utils/KernelUtils.sol

/// @audit 5
43:       for (uint256 i = 0; i < 5; ) {

/// @audit 0x41
/// @audit 0x5A
46:           if (char < 0x41 || char > 0x5A) revert InvalidKeycode(keycode_); // A-Z only

/// @audit 32
58:       for (uint256 i = 0; i < 32; ) {

/// @audit 0x61
/// @audit 0x7A
/// @audit 0x5f
/// @audit 0x00
60:           if ((char < 0x61 || char > 0x7A) && char != 0x5f && char != 0x00) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L43

### [N&#x2011;07]  Missing event and or timelock for critical parameter change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

*There are 3 instances of this issue:*
```solidity
File: src/Kernel.sol

126       function setActiveStatus(bool activate_) external onlyKernel {
127           isActive = activate_;
128:      }

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L126-L128

```solidity
File: src/policies/BondCallback.sol

190       function setOperator(Operator operator_) external onlyRole("callback_admin") {
191           if (address(operator_) == address(0)) revert Callback_InvalidParams();
192           operator = operator_;
193:      }

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L190-L193

```solidity
File: src/policies/Operator.sol

586       function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_)
587           external
588           onlyRole("operator_admin")
589       {
590           if (address(auctioneer_) == address(0) || address(callback_) == address(0))
591               revert Operator_InvalidParams();
592           /// Set contracts
593           auctioneer = auctioneer_;
594           callback = callback_;
595:      }

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L586-L595

### [N&#x2011;08]  Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

*There are 4 instances of this issue:*
```solidity
File: src/modules/RANGE.sol

/// @audit seen in src/modules/MINTR.sol 
68:       ERC20 public immutable ohm;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L68

```solidity
File: src/policies/Operator.sol

/// @audit seen in src/modules/RANGE.sol 
82:       ERC20 public immutable ohm;

/// @audit seen in src/modules/RANGE.sol 
85:       ERC20 public immutable reserve;

/// @audit seen in src/modules/RANGE.sol 
89:       uint32 public constant FACTOR_SCALE = 1e4;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L82

### [N&#x2011;09]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 8 instances of this issue:*
```solidity
File: src/modules/PRICE.sol

31:       /// @dev    Price feeds. Chainlink typically provides price feeds for an asset in ETH. Therefore, we use two price feeds against ETH, one for OHM and one for the Reserve asset, to calculate the relative price of OHM in the Reserve asset.

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L31

```solidity
File: src/modules/RANGE.sol

40:           uint256 spread; // Spread of the band (increase/decrease from the moving average to set the band prices), percent with 2 decimal places (i.e. 1000 = 10% spread)

46:           uint256 capacity; // Amount of tokens that can be used to defend the side of the range. Specified in OHM tokens on the high side and Reserve tokens on the low side.

61:       /// @notice Threshold factor for the change, a percent in 2 decimals (i.e. 1000 = 10%). Determines how much of the capacity must be spent before the side is taken down.

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L40

```solidity
File: src/policies/interfaces/IOperator.sol

15:           uint32 cushionDebtBuffer; // Percentage over the initial debt to allow the market to accumulate at any one time. Percent with 3 decimals, e.g. 1_000 = 1 %. See IBondAuctioneer for more info.

90:       /// @param  debtBuffer_ - Percentage over the initial debt to allow the market to accumulate at any one time. Percent with 3 decimals, e.g. 1_000 = 1 %. See IBondAuctioneer for more info.

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IOperator.sol#L15

```solidity
File: src/policies/Operator.sol

97:           uint32[8] memory configParams // [cushionFactor, cushionDuration, cushionDebtBuffer, cushionDepositInterval, reserveFactor, regenWait, regenThreshold, regenObserve]

657:          /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L97

### [N&#x2011;10]  Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables
If the variable needs to be different based on which class it comes from, a `view`/`pure` _function_ should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

*There are 18 instances of this issue:*
```solidity
File: src/policies/BondCallback.sol

29:       OlympusTreasury public TRSRY;

30:       OlympusMinter public MINTR;

68:           Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();

69:           Keycode MINTR_KEYCODE = MINTR.KEYCODE();

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L29

```solidity
File: src/policies/Governance.sol

56:       OlympusInstructions public INSTR;

57:       OlympusVotes public VOTES;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L56

```solidity
File: src/policies/Heart.sol

45:       OlympusPrice internal PRICE;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L45

```solidity
File: src/policies/Operator.sol

69:       OlympusPrice internal PRICE;

70:       OlympusRange internal RANGE;

71:       OlympusTreasury internal TRSRY;

72:       OlympusMinter internal MINTR;

172:          Keycode RANGE_KEYCODE = RANGE.KEYCODE();

173:          Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();

174:          Keycode MINTR_KEYCODE = MINTR.KEYCODE();

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L69

```solidity
File: src/policies/PriceConfig.sol

11:       OlympusPrice internal PRICE;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L11

```solidity
File: src/policies/TreasuryCustodian.sol

20:       OlympusTreasury internal TRSRY;

35:           Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L20

```solidity
File: src/policies/VoterRegistration.sol

10:       OlympusVotes public VOTES;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/VoterRegistration.sol#L10

### [N&#x2011;11]  Typos

*There are 4 instances of this issue:*
```solidity
File: src/interfaces/IBondCallback.sol

/// @audit _callback
13:       /// @dev Should check that the quote tokens have been transferred to the contract in the _callback function

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/interfaces/IBondCallback.sol#L13

```solidity
File: src/modules/PRICE.sol

/// @audit numbe
126:          // Cache numbe of observations to save gas.

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L126

```solidity
File: src/policies/Operator.sol

/// @audit deactive
295:              /// If wall is down after swap, deactive the cushion as well

/// @audit deactive
326:              /// If wall is down after swap, deactive the cushion as well

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L295

### [N&#x2011;12]  File is missing NatSpec

*There are 2 instances of this issue:*
```solidity
File: src/policies/TreasuryCustodian.sol


```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol

```solidity
File: src/utils/KernelUtils.sol


```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol

### [N&#x2011;13]  NatSpec is incomplete

*There are 7 instances of this issue:*
```solidity
File: src/modules/RANGE.sol

/// @audit Missing: '@return'
279       /// @notice Get the capacity for a side of the range.
280       /// @param  high_ - Specifies the side of the range to get capacity for (true = high side, false = low side).
281:      function capacity(bool high_) external view returns (uint256) {

/// @audit Missing: '@return'
289       /// @notice Get the status of a side of the range (whether it is active or not).
290       /// @param  high_ - Specifies the side of the range to get status for (true = high side, false = low side).
291:      function active(bool high_) external view returns (bool) {

/// @audit Missing: '@return'
300       /// @param  wall_ - Specifies the band to get the price for (true = wall, false = cushion).
301       /// @param  high_ - Specifies the side of the range to get the price for (true = high side, false = low side).
302:      function price(bool wall_, bool high_) external view returns (uint256) {

/// @audit Missing: '@return'
318       /// @notice Get the spread for the wall or cushion band.
319       /// @param  wall_ - Specifies the band to get the spread for (true = wall, false = cushion).
320:      function spread(bool wall_) external view returns (uint256) {

/// @audit Missing: '@return'
328       /// @notice Get the market ID for a side of the range.
329       /// @param  high_ - Specifies the side of the range to get market for (true = high side, false = low side).
330:      function market(bool high_) external view returns (uint256) {

/// @audit Missing: '@return'
338       /// @notice Get the timestamp when the range was last active.
339       /// @param  high_ - Specifies the side of the range to get timestamp for (true = high side, false = low side).
340:      function lastActive(bool high_) external view returns (uint256) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L279-L281

```solidity
File: src/policies/interfaces/IOperator.sol

/// @audit Missing: '@return'
141       /// @dev    Calculates the capacity to deploy for a wall based on the amount of reserves owned by the treasury and the reserve factor.
142       /// @param  high_ - Whether to return the full capacity for the high or low wall
143:      function fullCapacity(bool high_) external view returns (uint256);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IOperator.sol#L141-L143

### [N&#x2011;14]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 30 instances of this issue:*
```solidity
File: src/Kernel.sol

203       event PermissionsUpdated(
204           Keycode indexed keycode_,
205           Policy indexed policy_,
206           bytes4 funcSelector_,
207           bool granted_
208:      );

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L203-L208

```solidity
File: src/modules/INSTR.sol

11:       event InstructionsStored(uint256 instructionsId);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L11

```solidity
File: src/modules/PRICE.sol

26:       event NewObservation(uint256 timestamp_, uint256 price_, uint256 movingAverage_);

27:       event MovingAverageDurationChanged(uint48 movingAverageDuration_);

28:       event ObservationFrequencyChanged(uint48 observationFrequency_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L26

```solidity
File: src/modules/RANGE.sol

20:       event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);

21:       event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);

22:       event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);

23:       event CushionDown(bool high_, uint256 timestamp_);

24        event PricesChanged(
25            uint256 wallLowPrice_,
26            uint256 cushionLowPrice_,
27            uint256 cushionHighPrice_,
28            uint256 wallHighPrice_
29:       );

30:       event SpreadsChanged(uint256 cushionSpread_, uint256 wallSpread_);

31:       event ThresholdFactorChanged(uint256 thresholdFactor_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L20

```solidity
File: src/modules/TRSRY.sol

20:       event ApprovedForWithdrawal(address indexed policy_, ERC20 indexed token_, uint256 amount_);

27:       event DebtIncurred(ERC20 indexed token_, address indexed policy_, uint256 amount_);

28:       event DebtRepaid(ERC20 indexed token_, address indexed policy_, uint256 amount_);

29:       event DebtSet(ERC20 indexed token_, address indexed policy_, uint256 amount_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L20

```solidity
File: src/policies/Governance.sol

86:       event ProposalSubmitted(uint256 proposalId);

87:       event ProposalEndorsed(uint256 proposalId, address voter, uint256 amount);

88:       event ProposalActivated(uint256 proposalId, uint256 timestamp);

89:       event WalletVoted(uint256 proposalId, address voter, bool for_, uint256 userVotes);

90:       event ProposalExecuted(uint256 proposalId);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L86

```solidity
File: src/policies/Heart.sol

28:       event Beat(uint256 timestamp_);

29:       event RewardIssued(address to_, uint256 rewardAmount_);

30:       event RewardUpdated(ERC20 token_, uint256 rewardAmount_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L28

```solidity
File: src/policies/Operator.sol

45        event Swap(
46            ERC20 indexed tokenIn_,
47            ERC20 indexed tokenOut_,
48            uint256 amountIn_,
49            uint256 amountOut_
50:       );

51:       event CushionFactorChanged(uint32 cushionFactor_);

52:       event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);

53:       event ReserveFactorChanged(uint32 reserveFactor_);

54:       event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L45-L50

```solidity
File: src/policies/TreasuryCustodian.sol

17:       event ApprovalRevoked(address indexed policy_, ERC20[] tokens_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L17

### [N&#x2011;15]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 14 instances of this issue:*
```solidity
File: src/modules/INSTR.sol

/// @audit major
/// @audit minor
28:       function VERSION() public pure override returns (uint8 major, uint8 minor) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L28

```solidity
File: src/modules/MINTR.sol

/// @audit major
/// @audit minor
25:       function VERSION() external pure override returns (uint8 major, uint8 minor) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/MINTR.sol#L25

```solidity
File: src/modules/PRICE.sol

/// @audit major
/// @audit minor
113:      function VERSION() external pure override returns (uint8 major, uint8 minor) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L113

```solidity
File: src/modules/RANGE.sol

/// @audit major
/// @audit minor
115:      function VERSION() external pure override returns (uint8 major, uint8 minor) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L115

```solidity
File: src/modules/TRSRY.sol

/// @audit major
/// @audit minor
51:       function VERSION() external pure override returns (uint8 major, uint8 minor) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L51

```solidity
File: src/modules/VOTES.sol

/// @audit major
/// @audit minor
27:       function VERSION() external pure override returns (uint8 major, uint8 minor) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L27

```solidity
File: src/policies/BondCallback.sol

/// @audit in_
/// @audit out_
173       function amountsForMarket(uint256 id_)
174           external
175           view
176           override
177:          returns (uint256 in_, uint256 out_)

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L173-L177

### [N&#x2011;16]  Avoid the use of sensitive terms
Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist

*There is 1 instance of this issue:*
```solidity
File: /src/policies/BondCallback.sol

83:      function whitelist(address teller_, uint256 id_)

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L83



