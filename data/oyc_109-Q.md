## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
2022-08-olympus/src/policies/interfaces/IHeart.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus/src/policies/interfaces/IOperator.sol::2 => pragma solidity >=0.8.0;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-08-olympus/src/modules/PRICE.sol::143 => lastObservationTime = uint48(block.timestamp);
2022-08-olympus/src/modules/PRICE.sol::146 => emit NewObservation(block.timestamp, currentPrice, _movingAverage);
2022-08-olympus/src/modules/PRICE.sol::165 => if (updatedAt < block.timestamp - 3 * uint256(observationFrequency))
2022-08-olympus/src/modules/PRICE.sol::171 => if (updatedAt < block.timestamp - uint256(observationFrequency))
2022-08-olympus/src/modules/PRICE.sol::215 => if (startObservations_.length != numObs || lastObservationTime_ > uint48(block.timestamp))
2022-08-olympus/src/modules/RANGE.sol::85 => lastActive: uint48(block.timestamp),
2022-08-olympus/src/modules/RANGE.sol::92 => lastActive: uint48(block.timestamp),
2022-08-olympus/src/modules/RANGE.sol::136 => _range.high.lastActive = uint48(block.timestamp);
2022-08-olympus/src/modules/RANGE.sol::138 => emit WallDown(true, block.timestamp, capacity_);
2022-08-olympus/src/modules/RANGE.sol::148 => _range.low.lastActive = uint48(block.timestamp);
2022-08-olympus/src/modules/RANGE.sol::150 => emit WallDown(false, block.timestamp, capacity_);
2022-08-olympus/src/modules/RANGE.sol::191 => lastActive: uint48(block.timestamp),
2022-08-olympus/src/modules/RANGE.sol::200 => lastActive: uint48(block.timestamp),
2022-08-olympus/src/modules/RANGE.sol::207 => emit WallUp(high_, block.timestamp, capacity_);
2022-08-olympus/src/modules/RANGE.sol::231 => emit CushionDown(high_, block.timestamp);
2022-08-olympus/src/modules/RANGE.sol::233 => emit CushionUp(high_, block.timestamp, marketCapacity_);
2022-08-olympus/src/policies/Governance.sol::171 => block.timestamp,
2022-08-olympus/src/policies/Governance.sol::212 => if (block.timestamp > proposal.submissionTimestamp + ACTIVATION_DEADLINE) {
2022-08-olympus/src/policies/Governance.sol::227 => if (block.timestamp < activeProposal.activationTimestamp + GRACE_PERIOD) {
2022-08-olympus/src/policies/Governance.sol::231 => activeProposal = ActivatedProposal(proposalId_, block.timestamp);
2022-08-olympus/src/policies/Governance.sol::235 => emit ProposalActivated(proposalId_, block.timestamp);
2022-08-olympus/src/policies/Governance.sol::272 => if (block.timestamp < activeProposal.activationTimestamp + EXECUTION_TIMELOCK) {
2022-08-olympus/src/policies/Heart.sol::63 => lastBeat = block.timestamp;
2022-08-olympus/src/policies/Heart.sol::94 => if (block.timestamp < lastBeat + frequency()) revert Heart_OutOfCycle();
2022-08-olympus/src/policies/Heart.sol::108 => emit Beat(block.timestamp);
2022-08-olympus/src/policies/Heart.sol::131 => lastBeat = block.timestamp - frequency();
2022-08-olympus/src/policies/Operator.sol::128 => lastRegen: uint48(block.timestamp),
2022-08-olympus/src/policies/Operator.sol::210 => uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&
2022-08-olympus/src/policies/Operator.sol::216 => uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&
2022-08-olympus/src/policies/Operator.sol::404 => conclusion: uint48(block.timestamp + config_.cushionDuration),
2022-08-olympus/src/policies/Operator.sol::456 => conclusion: uint48(block.timestamp + config_.cushionDuration),
2022-08-olympus/src/policies/Operator.sol::708 => _status.high.lastRegen = uint48(block.timestamp);
2022-08-olympus/src/policies/Operator.sol::720 => _status.low.lastRegen = uint48(block.timestamp);
```

## [L-03] safeApprove() is deprecated

safeApprove() is deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead

```
2022-08-olympus/src/policies/BondCallback.sol::57 => ohm.safeApprove(address(MINTR), type(uint256).max);
2022-08-olympus/src/policies/Operator.sol::167 => ohm.safeApprove(address(MINTR), type(uint256).max);
```

## [L-04] Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

```
2022-08-olympus/src/policies/Operator.sol::657 => /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
2022-08-olympus/src/policies/TreasuryCustodian.sol::51 => // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
2022-08-olympus/src/policies/TreasuryCustodian.sol::52 => // TODO must reorg policy storage to be able to check for deactivated policies.
2022-08-olympus/src/policies/TreasuryCustodian.sol::56 => // TODO Make sure `policy_` is an actual policy and not a random address.
```

## [L-05] decimals() not part of ERC20 standard

decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```
2022-08-olympus/src/modules/PRICE.sol::84 => uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();
2022-08-olympus/src/modules/PRICE.sol::87 => uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();
2022-08-olympus/src/policies/Operator.sol::122 => ohmDecimals = tokens_[0].decimals();
2022-08-olympus/src/policies/Operator.sol::124 => reserveDecimals = tokens_[1].decimals();
2022-08-olympus/src/policies/Operator.sol::375 => uint256 oracleScale = 10**uint8(int8(PRICE.decimals()) - priceDecimals);
2022-08-olympus/src/policies/Operator.sol::418 => uint8 oracleDecimals = PRICE.decimals();
2022-08-olympus/src/policies/Operator.sol::493 => return decimals - int8(PRICE.decimals());
2022-08-olympus/src/policies/Operator.sol::754 => 10**ohmDecimals * 10**PRICE.decimals()
2022-08-olympus/src/policies/Operator.sol::764 => 10**ohmDecimals * 10**PRICE.decimals(),
2022-08-olympus/src/policies/Operator.sol::784 => 10**ohmDecimals * 10**PRICE.decimals(),
```

## [L-06] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-08-olympus/src/policies/Governance.sol::259 => VOTES.transferFrom(msg.sender, address(this), userVotes);
2022-08-olympus/src/policies/Governance.sol::312 => VOTES.transferFrom(address(this), msg.sender, userVotes);
```

## [L-07] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2022-08-olympus/src/Kernel.sol::126 => function setActiveStatus(bool activate_) external onlyKernel {
2022-08-olympus/src/policies/BondCallback.sol::190 => function setOperator(Operator operator_) external onlyRole("callback_admin") {
2022-08-olympus/src/policies/Operator.sol::498 => function setSpreads(uint256 cushionSpread_, uint256 wallSpread_)
2022-08-olympus/src/policies/Operator.sol::510 => function setThresholdFactor(uint256 thresholdFactor_) external onlyRole("operator_policy") {
2022-08-olympus/src/policies/Operator.sol::586 => function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_)
2022-08-olympus/src/policies/interfaces/IHeart.sol::34 => function setRewardTokenAndAmount(ERC20 token_, uint256 reward_) external;
2022-08-olympus/src/policies/interfaces/IOperator.sol::74 => function setSpreads(uint256 cushionSpread_, uint256 wallSpread_) external;
2022-08-olympus/src/policies/interfaces/IOperator.sol::80 => function setThresholdFactor(uint256 thresholdFactor_) external;
2022-08-olympus/src/policies/interfaces/IOperator.sol::85 => function setCushionFactor(uint32 cushionFactor_) external;
2022-08-olympus/src/policies/interfaces/IOperator.sol::92 => function setCushionParams(
2022-08-olympus/src/policies/interfaces/IOperator.sol::101 => function setReserveFactor(uint32 reserveFactor_) external;
2022-08-olympus/src/policies/interfaces/IOperator.sol::109 => function setRegenParams(
2022-08-olympus/src/policies/interfaces/IOperator.sol::119 => function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_) external;
```

## [L-08] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-08-olympus/src/modules/VOTES.sol::36 => _mint(wallet_, amount_);
```

## [L-09] Missing check for 0 transfer

Some ERC20 tokens will revert when transferring zero. A require statement should be added to check that `amount_` is not 0

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L82
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L99
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L110

## [L-10] Lack of zero address check

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/MINTR.sol#L16
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L102-L103
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L83
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L86
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L119-L121
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L43-L44

## [L-11] Unsafe cast

Precision may be lost when casting uint48 to uint32

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L97

## [L-12] RoundID not validatated

When getting prices using `latestRoundData()`, roundID should also be validated against answeredInRound 

eg
```
  (uint80 roundID, int256 _price, , uint256 updatedAt, uint80 answeredInRound) = feed.latestRoundData();
  require(answeredInRound >= roundID, "ChainLink: Stale price");
  require(updatedAt != 0, "ChainLink: Round not complete");
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L161
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L170

## [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
2022-08-olympus/src/policies/interfaces/IHeart.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus/src/policies/interfaces/IOperator.sol::2 => pragma solidity >=0.8.0;
```

## [N-02] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
2022-08-olympus/src/modules/RANGE.sol::245 => wallSpread_ > 10000 ||
2022-08-olympus/src/modules/RANGE.sol::247 => cushionSpread_ > 10000 ||
2022-08-olympus/src/modules/RANGE.sol::264 => if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();
2022-08-olympus/src/policies/Governance.sol::164 => if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
2022-08-olympus/src/policies/Operator.sol::111 => if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();
2022-08-olympus/src/policies/Operator.sol::518 => if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();
2022-08-olympus/src/policies/Operator.sol::550 => if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();
```

## [N-03] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-08-olympus/src/modules/INSTR.sol::11 => event InstructionsStored(uint256 instructionsId);
2022-08-olympus/src/modules/PRICE.sol::26 => event NewObservation(uint256 timestamp_, uint256 price_, uint256 movingAverage_);
2022-08-olympus/src/modules/PRICE.sol::27 => event MovingAverageDurationChanged(uint48 movingAverageDuration_);
2022-08-olympus/src/modules/PRICE.sol::28 => event ObservationFrequencyChanged(uint48 observationFrequency_);
2022-08-olympus/src/modules/RANGE.sol::20 => event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);
2022-08-olympus/src/modules/RANGE.sol::21 => event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);
2022-08-olympus/src/modules/RANGE.sol::22 => event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);
2022-08-olympus/src/modules/RANGE.sol::23 => event CushionDown(bool high_, uint256 timestamp_);
2022-08-olympus/src/modules/RANGE.sol::30 => event SpreadsChanged(uint256 cushionSpread_, uint256 wallSpread_);
2022-08-olympus/src/modules/RANGE.sol::31 => event ThresholdFactorChanged(uint256 thresholdFactor_);
2022-08-olympus/src/policies/Governance.sol::86 => event ProposalSubmitted(uint256 proposalId);
2022-08-olympus/src/policies/Governance.sol::87 => event ProposalEndorsed(uint256 proposalId, address voter, uint256 amount);
2022-08-olympus/src/policies/Governance.sol::88 => event ProposalActivated(uint256 proposalId, uint256 timestamp);
2022-08-olympus/src/policies/Governance.sol::89 => event WalletVoted(uint256 proposalId, address voter, bool for_, uint256 userVotes);
2022-08-olympus/src/policies/Governance.sol::90 => event ProposalExecuted(uint256 proposalId);
2022-08-olympus/src/policies/Heart.sol::28 => event Beat(uint256 timestamp_);
2022-08-olympus/src/policies/Heart.sol::29 => event RewardIssued(address to_, uint256 rewardAmount_);
2022-08-olympus/src/policies/Heart.sol::30 => event RewardUpdated(ERC20 token_, uint256 rewardAmount_);
2022-08-olympus/src/policies/Operator.sol::51 => event CushionFactorChanged(uint32 cushionFactor_);
2022-08-olympus/src/policies/Operator.sol::52 => event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);
2022-08-olympus/src/policies/Operator.sol::53 => event ReserveFactorChanged(uint32 reserveFactor_);
2022-08-olympus/src/policies/Operator.sol::54 => event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);
```

## [N-04] Missing NatSpec

Code should include NatSpec

```
2022-08-olympus/src/policies/TreasuryCustodian.sol::1 => // SPDX-License-Identifier: AGPL-3.0
2022-08-olympus/src/utils/KernelUtils.sol::1 => // SPDX-License-Identifier: AGPL-3.0-only
```

## [N-05] Constants should be defined rather than using magic numbers

It is bad practice to use numbers directly in code without explanation

```
2022-08-olympus/src/modules/PRICE.sol::90 => if (exponent > 38) revert Price_InvalidParams();
2022-08-olympus/src/modules/RANGE.sol::245 => wallSpread_ > 10000 ||
2022-08-olympus/src/modules/RANGE.sol::246 => wallSpread_ < 100 ||
2022-08-olympus/src/modules/RANGE.sol::247 => cushionSpread_ > 10000 ||
2022-08-olympus/src/modules/RANGE.sol::248 => cushionSpread_ < 100 ||
2022-08-olympus/src/modules/RANGE.sol::264 => if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();
2022-08-olympus/src/policies/Governance.sol::164 => if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
2022-08-olympus/src/policies/Governance.sol::217 => (totalEndorsementsForProposal[proposalId_] * 100) <
2022-08-olympus/src/policies/Governance.sol::268 => if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {
2022-08-olympus/src/policies/Operator.sol::111 => if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();
2022-08-olympus/src/policies/Operator.sol::378 => 36 + scaleAdjustment + int8(reserveDecimals) - int8(ohmDecimals) - priceDecimals
2022-08-olympus/src/policies/Operator.sol::433 => 36 + scaleAdjustment + int8(ohmDecimals) - int8(reserveDecimals) - priceDecimals
2022-08-olympus/src/policies/Operator.sol::518 => if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();
2022-08-olympus/src/policies/Operator.sol::550 => if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();
2022-08-olympus/src/utils/KernelUtils.sol::58 => for (uint256 i = 0; i < 32; ) {
```

## [N-06] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public.

```
2022-08-olympus/src/Kernel.sol::439 => function grantRole(Role role_, address addr_) public onlyAdmin {
2022-08-olympus/src/Kernel.sol::451 => function revokeRole(Role role_, address addr_) public onlyAdmin {
2022-08-olympus/src/modules/INSTR.sol::28 => function VERSION() public pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/INSTR.sol::37 => function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {
2022-08-olympus/src/modules/MINTR.sol::20 => function KEYCODE() public pure override returns (Keycode) {
2022-08-olympus/src/modules/MINTR.sol::33 => function mintOhm(address to_, uint256 amount_) public permissioned {
2022-08-olympus/src/modules/MINTR.sol::37 => function burnOhm(address from_, uint256 amount_) public permissioned {
2022-08-olympus/src/modules/PRICE.sol::108 => function KEYCODE() public pure override returns (Keycode) {
2022-08-olympus/src/modules/RANGE.sol::110 => function KEYCODE() public pure override returns (Keycode) {
2022-08-olympus/src/modules/TRSRY.sol::47 => function KEYCODE() public pure override returns (Keycode) {
2022-08-olympus/src/modules/VOTES.sol::22 => function KEYCODE() public pure override returns (Keycode) {
2022-08-olympus/src/modules/VOTES.sol::45 => function transfer(address to_, uint256 amount_) public pure override returns (bool) {
2022-08-olympus/src/policies/Governance.sol::145 => function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {
2022-08-olympus/src/policies/Governance.sol::151 => function getActiveProposal() public view returns (ActivatedProposal memory) {
```

## [N-07] Adding a return statement when the function defines a named return variable, is redundant

It is not necessary to have both a named return and a return statement.

```
2022-08-olympus/src/modules/INSTR.sol::28 => function VERSION() public pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/MINTR.sol::25 => function VERSION() external pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/PRICE.sol::113 => function VERSION() external pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/RANGE.sol::115 => function VERSION() external pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/TRSRY.sol::51 => function VERSION() external pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/VOTES.sol::27 => function VERSION() external pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/policies/BondCallback.sol::177 => returns (uint256 in_, uint256 out_)
```

## [N-08] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2022-08-olympus/src/Kernel.sol::126 => function setActiveStatus(bool activate_) external onlyKernel {
2022-08-olympus/src/policies/BondCallback.sol::190 => function setOperator(Operator operator_) external onlyRole("callback_admin") {
2022-08-olympus/src/policies/Operator.sol::498 => function setSpreads(uint256 cushionSpread_, uint256 wallSpread_)
2022-08-olympus/src/policies/Operator.sol::510 => function setThresholdFactor(uint256 thresholdFactor_) external onlyRole("operator_policy") {
2022-08-olympus/src/policies/Operator.sol::586 => function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_)
2022-08-olympus/src/policies/interfaces/IHeart.sol::34 => function setRewardTokenAndAmount(ERC20 token_, uint256 reward_) external;
2022-08-olympus/src/policies/interfaces/IOperator.sol::74 => function setSpreads(uint256 cushionSpread_, uint256 wallSpread_) external;
2022-08-olympus/src/policies/interfaces/IOperator.sol::80 => function setThresholdFactor(uint256 thresholdFactor_) external;
2022-08-olympus/src/policies/interfaces/IOperator.sol::85 => function setCushionFactor(uint32 cushionFactor_) external;
2022-08-olympus/src/policies/interfaces/IOperator.sol::92 => function setCushionParams(
2022-08-olympus/src/policies/interfaces/IOperator.sol::101 => function setReserveFactor(uint32 reserveFactor_) external;
2022-08-olympus/src/policies/interfaces/IOperator.sol::109 => function setRegenParams(
2022-08-olympus/src/policies/interfaces/IOperator.sol::119 => function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_) external;
```

## [N-09] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2022-08-olympus/src/modules/PRICE.sol::31 => /// @dev    Price feeds. Chainlink typically provides price feeds for an asset in ETH. Therefore, we use two price feeds against ETH, one for OHM and one for the Reserve asset, to calculate the relative price of OHM in the Reserve asset.
2022-08-olympus/src/modules/RANGE.sol::40 => uint256 spread; // Spread of the band (increase/decrease from the moving average to set the band prices), percent with 2 decimal places (i.e. 1000 = 10% spread)
2022-08-olympus/src/modules/RANGE.sol::46 => uint256 capacity; // Amount of tokens that can be used to defend the side of the range. Specified in OHM tokens on the high side and Reserve tokens on the low side.
2022-08-olympus/src/modules/RANGE.sol::61 => /// @notice Threshold factor for the change, a percent in 2 decimals (i.e. 1000 = 10%). Determines how much of the capacity must be spent before the side is taken down.
2022-08-olympus/src/policies/Operator.sol::97 => uint32[8] memory configParams // [cushionFactor, cushionDuration, cushionDebtBuffer, cushionDepositInterval, reserveFactor, regenWait, regenThreshold, regenObserve]
2022-08-olympus/src/policies/Operator.sol::657 => /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
2022-08-olympus/src/policies/interfaces/IOperator.sol::15 => uint32 cushionDebtBuffer; // Percentage over the initial debt to allow the market to accumulate at any one time. Percent with 3 decimals, e.g. 1_000 = 1 %. See IBondAuctioneer for more info.
2022-08-olympus/src/policies/interfaces/IOperator.sol::90 => /// @param  debtBuffer_ - Percentage over the initial debt to allow the market to accumulate at any one time. Percent with 3 decimals, e.g. 1_000 = 1 %. See IBondAuctioneer for more info.
```
