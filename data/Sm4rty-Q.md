
## 1. _safeMint() should be used rather than _mint() wherever possible

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

### Instances
[VOTES.sol:L36](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L36)
```
src/modules/VOTES.sol:36:        _mint(wallet_, amount_);
``` 
### Recommendations:

Use _safeMint() instead of _mint().

-----

## 2. Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

### Instances:
[Operator.sol:L657](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L657)
[TreasuryCustodian.sol:L51](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L51)
[TreasuryCustodian.sol:L52](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L52)
[TreasuryCustodian.sol:L56](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L56)
```
src/policies/Operator.sol:657:        /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
src/policies/TreasuryCustodian.sol:51:    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
src/policies/TreasuryCustodian.sol:52:    // TODO must reorg policy storage to be able to check for deactivated policies.
src/policies/TreasuryCustodian.sol:56:        // TODO Make sure `policy_` is an actual policy and not a random address.
``` 
### References:

[https://code4rena.com/reports/2022-05-sturdy/#l-09-open-todos](https://code4rena.com/reports/2022-05-sturdy/#l-09-open-todos)

-----


### 3. safeApprove() is Deprecated.

Deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead

Using this deprecated function can lead to unintended reverts and potentially the locking of funds. A deeper discussion on the deprecation of this function is in OZ issue [#2219](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2219). The OpenZeppelin ERC20 safeApprove() function has been deprecated, as seen in the comments of the OpenZeppelin code.

### Instances:
[BondCallback.sol:L57](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L57)
[Operator.sol:L167](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L167)
```
src/policies/BondCallback.sol:57:        ohm.safeApprove(address(MINTR), type(uint256).max);
src/policies/Operator.sol:167:        ohm.safeApprove(address(MINTR), type(uint256).max);
``` 
### References:

[https://code4rena.com/reports/2022-02-hubble/#l-07-deprecated-safeapprove-function](https://code4rena.com/reports/2022-02-hubble/#l-07-deprecated-safeapprove-function)


-----

## 4. Variable names that consist of all capital letters should be reserved for const/immutable variables

If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like this).

### Instances:
https://github.com/code-423n4/2022-08-olympus/blob/main/
https://github.com/code-423n4/2022-08-olympus/blob/main/
https://github.com/code-423n4/2022-08-olympus/blob/main/
```
src/policies/Heart.sol:45:    OlympusPrice internal PRICE;
src/policies/PriceConfig.sol:11:    OlympusPrice internal PRICE;
src/policies/Operator.sol:69:    OlympusPrice internal PRICE;
```

### References:

[https://code4rena.com/reports/2022-05-sturdy#n-08-variable-names-that-consist-of-all-capital-letters-should-be-reserved-for-constimmutable-variables](https://code4rena.com/reports/2022-05-sturdy#n-08-variable-names-that-consist-of-all-capital-letters-should-be-reserved-for-constimmutable-variables)

-----

## 5.Multiple initialization due to initialize function not having initializer modifier.

### Description

The attacker can initialize the contract, take malicious actions, and allow it to be re-initialized by the project without any error being noticed.
### Instances
[PriceConfig.sol:L45](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L45)
[Operator.sol:L598](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L598)
[IOperator.sol:L125](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L125)
```
// actual codes used
policies/PriceConfig.sol:45:    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
policies/Operator.sol:598:    function initialize() external onlyRole("operator_admin") {
policies/interfaces/IOperator.sol:125:    function initialize() external;
```
## 6. The non Reentrant modifier should occur before all other modifiers

### Instances
[Operator.sol:L276](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L276)
```
src/policies/Operator.sol:276:    ) external override onlyWhileActive nonReentrant returns (uint256 amountOut) {
```

### Recommended Mitigation Steps
using the nonReentrant modifier before onlyWhileActive modifier

-----
## 1. Use of Block.timestamp

`Impact - Non-Critical`

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Instances
[PRICE.sol:L143](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L143)
[PRICE.sol:L146](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L146)
[PRICE.sol:L165](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L165)
[PRICE.sol:L171](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L171)
[PRICE.sol:L215](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L215)
[RANGE.sol:L85](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L85)
[RANGE.sol:L92](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L92)
[RANGE.sol:L136](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L136)
[RANGE.sol:L138](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L138)
[RANGE.sol:L148](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L148)
[RANGE.sol:L150](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L150)
[RANGE.sol:L191](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L191)
[RANGE.sol:L200](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L200)
[RANGE.sol:L207](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L207)
[RANGE.sol:L231](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L231)
[RANGE.sol:L233](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L233)
[Heart.sol:L63](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L63)
[Heart.sol:L94](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L94)
[Heart.sol:L108](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L108)
[Heart.sol:L131](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L131)
[Operator.sol:L128](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L128)
[Operator.sol:L210](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L210)
[Operator.sol:L216](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L216)
[Operator.sol:L404](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L404)
[Operator.sol:L456](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L456)
[Operator.sol:L708](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L708)
[Operator.sol:L720](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L720)
[Governance.sol:L171](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L171)
[Governance.sol:L212](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L212)
[Governance.sol:L227](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L227)
[Governance.sol:L231](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L231)
[Governance.sol:L235](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L235)
[Governance.sol:L272](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L272)
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

-----

