### Misspell of comment

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L126-L127

```solidity
        // Cache numbe of observations to save gas.
        uint32 numObs = numObservations;
```

## Recommended Mitigation Steps

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L126-L127

```solidity
-       // Cache numbe of observations to save gas.
+       // Cache number of observations to save gas.
        uint32 numObs = numObservations;
```

### One step key roles change

## Proof of Concept

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L250-L253

```solidity
        } else if (action_ == Actions.ChangeExecutor) {
            executor = target_;
        } else if (action_ == Actions.ChangeAdmin) {
            admin = target_;
```

## Recommended Mitigation Steps

Consider utilizing two-step ownership transferring process (proposition and acceptance in the separate actions) with a noticeable delay between the steps to enforce the transparency and stability of the system.

### Non-current compiler version

## Proof of Concept

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/VoterRegistration.sol#L2

```solidity
pragma solidity 0.8.15;
```

## Recommended Mitigation Steps

Update to 0.8.16 as a bug was fixed in 0.8.15



### Hardcoded multiplier

## Proof of Concept

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L161-L166

```solidity
            (, int256 ohmEthPriceInt, , uint256 updatedAt, ) = _ohmEthPriceFeed.latestRoundData();
            // Use a multiple of observation frequency to determine what is too old to use.
            // Price feeds will not provide an updated answer if the data doesn't change much.
            // This would be similar to if the feed just stopped updating; therefore, we need a cutoff.
            if (updatedAt < block.timestamp - 3 * uint256(observationFrequency))
                revert Price_BadFeed(address(_ohmEthPriceFeed));
```

### Hardcoded boundaries

## Proof of Concept

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L237-L250

```solidity
    /// @notice Set the wall and cushion spreads.
    /// @notice Access restricted to activated policies.
    /// @param  cushionSpread_ - Percent spread to set the cushions at above/below the moving average, assumes 2 decimals (i.e. 1000 = 10%).
    /// @param  wallSpread_ - Percent spread to set the walls at above/below the moving average, assumes 2 decimals (i.e. 1000 = 10%).
    /// @dev    The new spreads will not go into effect until the next time updatePrices() is called.
    function setSpreads(uint256 cushionSpread_, uint256 wallSpread_) external permissioned {
        // Confirm spreads are within allowed values
        if (
            wallSpread_ > 10000 ||
            wallSpread_ < 100 ||
            cushionSpread_ > 10000 ||
            cushionSpread_ < 100 ||
            cushionSpread_ > wallSpread_
        ) revert RANGE_InvalidParams();
```

### Different threshold bases, which are hardcoded (non-critical)

Amount calculations can become incorrect with a range of impacts up to fund loss and insolvency if the values hard coded across the implementation mix up on future development.

## Proof of Concept

Numerical bases differ and are hardcoded across the logic:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L154-L165

```solidity

    /// @notice Submit an on chain governance proposal.
    /// @param  instructions_ - an array of Instruction objects each containing a Kernel Action and a target Contract address.
    /// @param  title_ - a human-readable title of the proposal — i.e. "OIP XX - My Proposal Title".
    /// @param  proposalURI_ - an arbitrary url linking to a human-readable description of the proposal - i.e. Snapshot, Discourse, Google Doc.
    function submitProposal(
        Instruction[] calldata instructions_,
        bytes32 title_,
        string memory proposalURI_
    ) external {
        if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
            revert NotEnoughVotesToPropose();
```


https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L216-L221

```solidity
        if (
            (totalEndorsementsForProposal[proposalId_] * 100) <
            VOTES.totalSupply() * ENDORSEMENT_THRESHOLD
        ) {
            revert NotEnoughEndorsementsToActivateProposal();
        }
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L264-L270

```solidity
    /// @notice Execute the currently active proposal.
    function executeProposal() external {
        uint256 netVotes = yesVotesForProposal[activeProposal.proposalId] -
            noVotesForProposal[activeProposal.proposalId];
        if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {
            revert NotEnoughVotesToExecute();
        }
```

## Recommended Mitigation Steps

Consider unifying the bases and introducing the bp constant, for example:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L119-L133

```solidity
    /// @notice The amount of votes a proposer needs in order to submit a proposal as a percentage of total supply (in basis points).
    /// @dev    This is set to 1% of the total supply.
    uint256 public constant SUBMISSION_REQUIREMENT = 100;

    /// @notice Amount of time a submitted proposal has to activate before it expires.
    uint256 public constant ACTIVATION_DEADLINE = 2 weeks;

    /// @notice Amount of time an activated proposal must stay up before it can be replaced by a new activated proposal.
    uint256 public constant GRACE_PERIOD = 1 weeks;

-   /// @notice Endorsements required to activate a proposal as percentage of total supply.
+   /// @notice Endorsements required to activate a proposal as percentage of total supply, in basis points.
-   uint256 public constant ENDORSEMENT_THRESHOLD = 20;
+   uint256 public constant ENDORSEMENT_THRESHOLD = 2000;

-   /// @notice Net votes required to execute a proposal on chain as a percentage of total supply.
+   /// @notice Net votes required to execute a proposal on chain as a percentage of total supply, in basis points.
-   uint256 public constant EXECUTION_THRESHOLD = 33;
+   uint256 public constant EXECUTION_THRESHOLD = 3300;


+   /// @notice Basis point base.
+   uint256 public constant BP_BASE = 10000;    
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L154-L165

```solidity

    /// @notice Submit an on chain governance proposal.
    /// @param  instructions_ - an array of Instruction objects each containing a Kernel Action and a target Contract address.
    /// @param  title_ - a human-readable title of the proposal — i.e. "OIP XX - My Proposal Title".
    /// @param  proposalURI_ - an arbitrary url linking to a human-readable description of the proposal - i.e. Snapshot, Discourse, Google Doc.
    function submitProposal(
        Instruction[] calldata instructions_,
        bytes32 title_,
        string memory proposalURI_
    ) external {
-       if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
+       if (VOTES.balanceOf(msg.sender) * BP_BASE < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
            revert NotEnoughVotesToPropose();
```


https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L216-L221

```solidity
        if (
-           (totalEndorsementsForProposal[proposalId_] * 100) <
+           (totalEndorsementsForProposal[proposalId_] * BP_BASE) <
            VOTES.totalSupply() * ENDORSEMENT_THRESHOLD
        ) {
            revert NotEnoughEndorsementsToActivateProposal();
        }
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L264-L270

```solidity
    /// @notice Execute the currently active proposal.
    function executeProposal() external {
        uint256 netVotes = yesVotesForProposal[activeProposal.proposalId] -
            noVotesForProposal[activeProposal.proposalId];
-       if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {
+       if (netVotes * BP_BASE < VOTES.totalSupply() * EXECUTION_THRESHOLD) {
            revert NotEnoughVotesToExecute();
        }
```

### _addObservation comments aren't fully correct (low)

_addObservation() low and high cases comments state more restrictive logic:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L664-L667

```solidity
        /// Observation is positive if the current price is greater than the MA
        uint32 observe = _config.regenObserve;
        Regen memory regen = _status.low;
        if (currentPrice >= movingAverage) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L681-L683

```solidity
        /// Observation is positive if the current price is less than the MA
        regen = _status.high;
        if (currentPrice <= movingAverage) {
```

## Recommended Mitigation Steps

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L664-L667

```solidity
-       /// Observation is positive if the current price is greater than the MA
+       /// Observation is positive if the current price not less than the MA
        uint32 observe = _config.regenObserve;
        Regen memory regen = _status.low;
        if (currentPrice >= movingAverage) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L681-L683

```solidity
-       /// Observation is positive if the current price is less than the MA
+       /// Observation is positive if the current price is not greater than the MA
        regen = _status.high;
        if (currentPrice <= movingAverage) {
```


### thresholdFactor isn't included to the WallUp and WallDown events

regenerate() makes a `thresholdFactor` effective after the change, but this isn't broadcasted in WallUp, i.e. the change is in fact performed silently.

## Proof of Concept

regenerate() omits new threshold in the broadcast, while it's both new `capacity_` and new `thresholdFactor` can be made effective at that moment:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L180-L185

```solidity
    /// @notice Regenerate a side of the range to a specific capacity.
    /// @notice Access restricted to activated policies.
    /// @param  high_ - Specifies the side of the range to regenerate (true = high side, false = low side).
    /// @param  capacity_ - Amount to set the capacity to (OHM tokens for high side, Reserve tokens for low side).
    function regenerate(bool high_, uint256 capacity_) external permissioned {
        uint256 threshold = (capacity_ * thresholdFactor) / FACTOR_SCALE;
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L207-L208

```solidity
        emit WallUp(high_, block.timestamp, capacity_);
    }
```

As it's only regenerate() makes current `thresholdFactor` effective after the change:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L262-L263

```solidity
    /// @dev    The new threshold factor will not go into effect until the next time regenerate() is called for each side of the wall.
    function setThresholdFactor(uint256 thresholdFactor_) external permissioned {
```

WallDown depends on the `capacity_` vs `threshold` situation, but also does not emit the current `threshold`:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L132-L139

```solidity
            // If the new capacity is below the threshold, deactivate the wall if they are currently active
            if (capacity_ < _range.high.threshold && _range.high.active) {
                // Set wall to inactive
                _range.high.active = false;
                _range.high.lastActive = uint48(block.timestamp);

                emit WallDown(true, block.timestamp, capacity_);
            }
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L144-L151

```solidity
            // If the new capacity is below the threshold, deactivate the wall if they are currently active
            if (capacity_ < _range.low.threshold && _range.low.active) {
                // Set wall to inactive
                _range.low.active = false;
                _range.low.lastActive = uint48(block.timestamp);

                emit WallDown(false, block.timestamp, capacity_);
            }
```

## Recommended Mitigation Steps

Consider adding the thresholds to enhance transparency:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L207-L208

```solidity
-       emit WallUp(high_, block.timestamp, capacity_);
+       emit WallUp(high_, block.timestamp, capacity_, threshold);
    }
```

```solidity
            // If the new capacity is below the threshold, deactivate the wall if they are currently active
            if (capacity_ < _range.high.threshold && _range.high.active) {
                // Set wall to inactive
                _range.high.active = false;
                _range.high.lastActive = uint48(block.timestamp);

-               emit WallDown(true, block.timestamp, capacity_);
+               emit WallDown(true, block.timestamp, capacity_, _range.high.threshold);
            }
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L144-L151

```solidity
            // If the new capacity is below the threshold, deactivate the wall if they are currently active
            if (capacity_ < _range.low.threshold && _range.low.active) {
                // Set wall to inactive
                _range.low.active = false;
                _range.low.lastActive = uint48(block.timestamp);

-               emit WallDown(false, block.timestamp, capacity_);
+               emit WallDown(false, block.timestamp, capacity_, _range.low.threshold);
            }
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L20-L21

```solidity
-   event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);
-   event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);
+   event WallUp(bool high_, uint256 timestamp_, uint256 capacity_, uint256 threshold_);
+   event WallDown(bool high_, uint256 timestamp_, uint256 capacity_, uint256 threshold_);
```

