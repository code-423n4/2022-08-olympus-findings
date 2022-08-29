
# Olympus V3 Findings

### **Repo that implements suggested changes:** [0xClandestine/2022-08-olympus](https://github.com/0xClandestine/2022-08-olympus)

**Severity:** *Gas Optimization*

**Context:** [INSTR.sol#L44](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L44), [PRICE.sol#144](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L144), [PRICE.sol#135](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#135), [Governance.sol#L251](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L251), [Heart.sol#L92](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L92)


**Description:** Arithmetic checks aren't necessary when logic cannot realistically underflow/overflow.

**Recommendation:** 
[INSTR.sol#L44](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L44)
```solidity
-    uint256 instructionsId = ++totalInstructions;
+    uint256 instructionsId;
+
+    unchecked {
+        instructionsId = ++totalInstructions;
+    }
```

**Recommendation:** 
[PRICE.sol#144](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L144)
```solidity
+    uint256 nextObs = nextObsIndex; // should cache this value

...

-    nextObsIndex = (nextObsIndex + 1) % numObs;
+
+    unchecked {
+        ++nextObs;
+    }
+
+    nextObsIndex = nextObs % numObs;
```

**Recommendation:** 
[PRICE.sol#135](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L135)
```solidity

    // overflow/underflow is impossible here because the conditional explicitly checks the arithmetic.
    if (currentPrice > earliestPrice) {
-        _movingAverage += (currentPrice - earliestPrice) / numObs;
+
+        unchecked {
+            priceDelta = currentPrice - earliestPrice;
+        }
+
+        _movingAverage += priceDelta / numObs;
    } else {
-        _movingAverage -= (earliestPrice - currentPrice) / numObs;
+
+        unchecked {
+            priceDelta = earliestPrice - currentPrice;
+        }
+
+        _movingAverage -= priceDelta / numObs;
+    }
```

**Recommendation:** [Governance.sol#L251](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L251)

```solidity
-    if (for_) {
-        yesVotesForProposal[activeProposal.proposalId] += userVotes;
-    } else {
-        noVotesForProposal[activeProposal.proposalId] += userVotes;
-    }
    
+    // total votes cannot exceed totalSupply
+    unchecked {
+        if (for_) {
+            yesVotesForProposal[activeProposal.proposalId] += userVotes;
+        } else {
+            noVotesForProposal[activeProposal.proposalId] += userVotes;
+        }
+    }
```

**Recommendation:** [Heart.sol#L92](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L92)

**Note:** Consider setting lastBeat + frequency as an unchecked variable to avoid calculating it 3 times.

```solidity
function beat() external nonReentrant {
    if (!active) revert Heart_BeatStopped();

-    if (block.timestamp < lastBeat + frequency()) 
-        revert Heart_OutOfCycle();

+   unchecked {
+       if (block.timestamp < lastBeat + frequency()) 
+            revert Heart_OutOfCycle();
+   }

    // Update the moving average on the Price module
    PRICE.updateMovingAverage();

    // Trigger price range update and market operations
    _operator.operate();

    // Update the last beat timestamp
-    lastBeat += frequency();

+    unchecked {
+        lastBeat += frequency();
+    }

    // Issue reward to sender
    _issueReward(msg.sender);

    emit Beat(block.timestamp);
}
```
___

**Severity:** *Gas Optimization*

**Context:** [PRICE.sol#L252](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L252), [PRICE.sol#L284](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L284)

**Description:** Use [delete](https://docs.soliditylang.org/en/v0.8.0/types.html#delete) keyword when mutating state variables back to null/zero value to receive a gas refund.

**Recommendation:** 
[PRICE.sol#L252](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L252)
```solidity
-    initialized = false;
-    lastObservationTime = 0;
-    _movingAverage = 0;
-    nextObsIndex = 0;

+    delete initialized;
+    delete lastObservationTime;
+    delete _movingAverage;
+    delete nextObsIndex;
```

**Recommendation:** 
[PRICE.sol#L284](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L284)
```solidity
-    initialized = false;
-    lastObservationTime = 0;
-    _movingAverage = 0;
-    nextObsIndex = 0;

+    delete initialized;
+    delete lastObservationTime;
+    delete _movingAverage;
+    delete nextObsIndex;
```

___

**Severity:** *Gas Optimization*

**Context:** [Governance.sol#L278](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L278), [PRICE.sol#L122](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L122), [Heart.sol#L92](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L92)

**Description:** Cache state variables and array lengths before readings them multiple times (like in a loop).

**Recommendation:** [Governance.sol#L278](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L278)
```solidity
-    for (uint256 step; step < instructions.length; ) {
    
+    uint256 instructionsLength = instructions.length;
+
+    for (uint256 step; step < instructionsLength;) {
```

**Recommendation:** [PRICE.sol#L122](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L122)
```solidity

// 2 SLOADs saved

function updateMovingAverage() external permissioned {
    // Revert if not initialized
    if (!initialized) revert Price_NotInitialized();

    // Cache number of observations to save gas.
    uint32 numObs = numObservations;

+    // Cache next observation index to save gas.
+    uint256 nextObs = nextObsIndex; // avoid SLOADs

    // Get earliest observation in window
-   uint256 earliestPrice = observations[nextObsIndex];
+   uint256 earliestPrice = observations[nextObs]; // avoid SLOAD

    uint256 currentPrice = getCurrentPrice();

    // Calculate new moving average
    if (currentPrice > earliestPrice) {
        _movingAverage += (currentPrice - earliestPrice) / numObs;
    } else {
        _movingAverage -= (earliestPrice - currentPrice) / numObs;
    }

    // Push new observation into storage and store timestamp taken at
-    observations[nextObsIndex] = currentPrice;
+    observations[nextObs] = currentPrice; // avoid SLOAD
    lastObservationTime = uint48(block.timestamp);
-    nextObsIndex = (nextObsIndex + 1) % numObs;
+    nextObsIndex = (nextObs + 1) % numObs; // avoid SLOAD

    emit NewObservation(block.timestamp, currentPrice, _movingAverage);
}
```


**Recommendation:** [Heart.sol#L92](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L92)

```solidity

// 2 SLOADs saved

function beat() external nonReentrant {
    if (!active) revert Heart_BeatStopped();

+    uint256 _lastBeat = lastBeat;

-    if (block.timestamp < lastBeat + frequency()) revert Heart_OutOfCycle();
+    if (block.timestamp < _lastBeat + frequency()) revert Heart_OutOfCycle();

    // Update the moving average on the Price module
    PRICE.updateMovingAverage();

    // Trigger price range update and market operations
    _operator.operate();

    // Update the last beat timestamp
-    lastBeat += frequency();
+    lastBeat = _lastBeat + frequency(); // += causes another SLOAD

    // Issue reward to sender
    _issueReward(msg.sender);

    emit Beat(block.timestamp);
}

```
___

**Severity:** *Gas Optimization*

**Context:** [Governance.sol#L194](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L194)

**Description:** Mutating a single slot multiple times in a function should be avoided when possible.

**Recommendation:** [Governance.sol#L194](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L194)

**Note:** a = a + b or a = a - b is slightly cheaper than a += b or a -= b when mutating mappings. This can be applied other places in the codebase as well.

```solidity

// 1 SSTORE saved

function endorseProposal(uint256 proposalId_) external {
    uint256 userVotes = VOTES.balanceOf(msg.sender);

    if (proposalId_ == 0) {
        revert CannotEndorseNullProposal();
    }

    Instruction[] memory instructions = INSTR.getInstructions(proposalId_);
    if (instructions.length == 0) {
        revert CannotEndorseInvalidProposal();
    }

    // undo any previous endorsement the user made on these instructions
    uint256 previousEndorsement = userEndorsementsForProposal[proposalId_][msg.sender];
-    totalEndorsementsForProposal[proposalId_] -= previousEndorsement;

    // reapply user endorsements with most up-to-date votes
    userEndorsementsForProposal[proposalId_][msg.sender] = userVotes;
-    totalEndorsementsForProposal[proposalId_] += userVotes;
+    totalEndorsementsForProposal[proposalId_] = totalEndorsementsForProposal[proposalId_] - previousEndorsement + userVotes; // this can potentially be unchecked

    emit ProposalEndorsed(proposalId_, msg.sender, userVotes);
}
```