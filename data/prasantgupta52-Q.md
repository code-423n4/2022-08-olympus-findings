## 1. safeApprove() is deprecated
### Description
Deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance().Whenever possible, use {safeIncreaseAllowance} and {safeDecreaseAllowance} instead.

### Instances
//Links to github Files:
[Operator.sol:L167](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L167)
[BondCallback.sol:L57](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L57)


*Actual Codes Used*

```
src/policies/Operator.sol:167:        ohm.safeApprove(address(MINTR), type(uint256).max);
src/policies/BondCallback.sol:57:        ohm.safeApprove(address(MINTR), type(uint256).max);
```
----
## 2. Avoid using Floating Pragma:
### Description
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
The pragma declared across the solution is ^0.8.0
As the compiler introduces a several interesting upgrades in newer  versions of Solidity
consider locking at this version or a more recent one.
### Instances
//Links to github Files:
[IBondCallback.sol:L2](https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L2)
[IOperator.sol:L2](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L2)
[IHeart.sol:L2](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#L2)


*Actual Codes Used*

```
src/interfaces/IBondCallback.sol:2:pragma solidity >=0.8.0;
src/policies/interfaces/IOperator.sol:2:pragma solidity >=0.8.0;
src/policies/interfaces/IHeart.sol:2:pragma solidity >=0.8.0;

```
----

## 3. The nonReentrant modifier should occur before all other modifiers
### Description
The nonReentrant modifier should occur before all other modifiers
This is a best-practice to protect against re-entrancy in other modifiers
### Instances 
// Links To Github Files:
[Operator.sol:L276](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L276)

*Actual Codes Used*

```
src/policies/Operator.sol:276:    ) external override onlyWhileActive nonReentrant returns (uint256 amountOut) {
```
### Recommended Mitigation Steps
using the nonReentrant modifier before onlyWhileActive modifier

----

## 4. _safeMint() should be used rather than _mint() wherever possible
### Description
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`.
### Instances 
// Links To Github Files:
[VOTES.sol:L36](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L36)


*Actual Codes Used*
```
src/modules/VOTES.sol:36:        _mint(wallet_, amount_);

```

### Recommendations:

Use _safeMint() instead of _mint().

----
## 5. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom
### Description
It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

### Instances
//Links to github Files:
[Governance.sol:L79](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L79)
[Governance.sol:L259](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L259)
[Governance.sol:L312](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L312)

*Actual Codes Used*
```

src/policies/Governance.sol:79:        requests[1] = Permissions(VOTES.KEYCODE(), VOTES.transferFrom.selector);
src/policies/Governance.sol:259:        VOTES.transferFrom(msg.sender, address(this), userVotes);
src/policies/Governance.sol:312:        VOTES.transferFrom(address(this), msg.sender, userVotes);
```
### Recommended Mitigation Steps

Consider using safeTransfer/safeTransferFrom or require() consistently.

----
## 6.Multiple initialization due to initialize function not having initializer modifier.

### Description

The attacker can initialize the contract, take malicious actions, and allow it to be re-initialized by the project without any error being noticed.
### Instances 
// Links to githubfile
[PriceConfig.sol:L45](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45)
[Operator.sol:L598](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L598)
[IOperator.sol:L125](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L125)


*Actual Codes Used*
```
src/policies/PriceConfig.sol:45:    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
src/policies/Operator.sol:598:    function initialize() external onlyRole("operator_admin") {
src/policies/interfaces/IOperator.sol:125:    function initialize() external;
```
----

## 7. Open TODOs
### Description
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
### Instances
// Link to Github files:
[Operator.sol:L657](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L657)
[TreasuryCustodian.sol:L51](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L51)
[TreasuryCustodian.sol:L52](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L52)
[TreasuryCustodian.sol:L56](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L56)

*Actual Codes Used*

```
src/policies/Operator.sol:657:        /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
src/policies/TreasuryCustodian.sol:51:    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
src/policies/TreasuryCustodian.sol:52:    // TODO must reorg policy storage to be able to check for deactivated policies.
src/policies/TreasuryCustodian.sol:56:        // TODO Make sure `policy_` is an actual policy and not a random address.
```
----
## 8. Use of Block.timestamp
Impact - Non-Critical
### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Instances
// Link to Github files:
[PRICE.sol:L143](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L143)
[PRICE.sol:L146](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L146)
[PRICE.sol:L165](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L165)
[PRICE.sol:L171](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L171)
[PRICE.sol:L215](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L215)
[RANGE.sol:L85](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L85)
[RANGE.sol:L92](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L92)
[RANGE.sol:L136](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L136)
[RANGE.sol:L138](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L138)
[RANGE.sol:L148](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L148)
[RANGE.sol:L150](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L150)
[RANGE.sol:L191](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L191)
[RANGE.sol:L200](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L200)
[RANGE.sol:L207](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L207)
[RANGE.sol:L231](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L231)
[RANGE.sol:L233](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L233)
[Heart.sol:L63](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L63)
[Heart.sol:L94](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L94)
[Heart.sol:L108](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L108)
[Heart.sol:L131](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L131)
[Operator.sol:L128](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L128)
[Operator.sol:L210](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L210)
[Operator.sol:L216](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L216)
[Operator.sol:L404](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L404)
[Operator.sol:L456](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L456)
[Operator.sol:L708](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L708)
[Operator.sol:L720](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L720)
[Governance.sol:L171](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L171)
[Governance.sol:L212](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L212)
[Governance.sol:L227](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L227)
[Governance.sol:L231](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L231)
[Governance.sol:L235](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L235)
[Governance.sol:L272](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L272)

*Actual Codes Used*
```
src/modules/PRICE.sol:143:        lastObservationTime = uint48(block.timestamp);
src/modules/PRICE.sol:146:        emit NewObservation(block.timestamp, currentPrice, _movingAverage);
src/modules/PRICE.sol:165:            if (updatedAt < block.timestamp - 3 * uint256(observationFrequency))
src/modules/PRICE.sol:171:            if (updatedAt < block.timestamp - uint256(observationFrequency))
src/modules/PRICE.sol:215:        if (startObservations_.length != numObs || lastObservationTime_ > uint48(block.timestamp))
src/modules/RANGE.sol:85:                lastActive: uint48(block.timestamp),
src/modules/RANGE.sol:92:                lastActive: uint48(block.timestamp),
src/modules/RANGE.sol:136:                _range.high.lastActive = uint48(block.timestamp);
src/modules/RANGE.sol:138:                emit WallDown(true, block.timestamp, capacity_);
src/modules/RANGE.sol:148:                _range.low.lastActive = uint48(block.timestamp);
src/modules/RANGE.sol:150:                emit WallDown(false, block.timestamp, capacity_);
src/modules/RANGE.sol:191:                lastActive: uint48(block.timestamp),
src/modules/RANGE.sol:200:                lastActive: uint48(block.timestamp),
src/modules/RANGE.sol:207:        emit WallUp(high_, block.timestamp, capacity_);
src/modules/RANGE.sol:231:            emit CushionDown(high_, block.timestamp);
src/modules/RANGE.sol:233:            emit CushionUp(high_, block.timestamp, marketCapacity_);
src/policies/Heart.sol:63:        lastBeat = block.timestamp;
src/policies/Heart.sol:94:        if (block.timestamp < lastBeat + frequency()) revert Heart_OutOfCycle();
src/policies/Heart.sol:108:        emit Beat(block.timestamp);
src/policies/Heart.sol:131:        lastBeat = block.timestamp - frequency();
src/policies/Operator.sol:128:            lastRegen: uint48(block.timestamp),
src/policies/Operator.sol:210:            uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&
src/policies/Operator.sol:216:            uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&
src/policies/Operator.sol:404:                conclusion: uint48(block.timestamp + config_.cushionDuration),
src/policies/Operator.sol:456:                conclusion: uint48(block.timestamp + config_.cushionDuration),
src/policies/Operator.sol:708:            _status.high.lastRegen = uint48(block.timestamp);
src/policies/Operator.sol:720:            _status.low.lastRegen = uint48(block.timestamp);
src/policies/Governance.sol:171:            block.timestamp,
src/policies/Governance.sol:212:        if (block.timestamp > proposal.submissionTimestamp + ACTIVATION_DEADLINE) {
src/policies/Governance.sol:227:        if (block.timestamp < activeProposal.activationTimestamp + GRACE_PERIOD) {
src/policies/Governance.sol:231:        activeProposal = ActivatedProposal(proposalId_, block.timestamp);
src/policies/Governance.sol:235:        emit ProposalActivated(proposalId_, block.timestamp);
src/policies/Governance.sol:272:        if (block.timestamp < activeProposal.activationTimestamp + EXECUTION_TIMELOCK) {
```
### Recommended Mitigation Steps

Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

----