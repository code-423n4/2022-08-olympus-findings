  # Gas Optimizations

## Summary Of Findings:

  | Issue 
-- | -- 
1 | Caching storage variable and using `unchecked` in `updateMovingAverage()` function
2 | Simplify formulas and emit local variables in `updatePrices` function
3 | Caching storage variable in the `callback` function
4 | Caching storage variable in the `vote` function
5 | Caching storage variable in the `executeProposal` function

## Detailed Report on Gas Optimization Findings:

### 1. <ins>Caching storage variable and using `unchecked` in `updateMovingAverage()` function</ins>
The [updateMovingAverage()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L122-L147) function in `PRICE.sol` can save gas by the following changes:

 1. Cache the state variable `nextObsIndex`. Storage reads are much more expensive than memory reads (100 Vs 3).
 2. Use uncheck block for the line `nextObsIndex = (nextObsIndex + 1) % numObs;`
 As `numObs` is greater than zero from the previous calculation in the if-else block. And `nextObsIndex` can be safely assumed to be never equal to `type(uint32).max`.
 
 The following `diff` shows the mitigation:
```diff
diff --git "a/.\\orig.txt" "b/.\\modified.txt"
index 7b37684..ca3cab4 100644
--- "a/.\\orig.txt"
+++ "b/.\\modified.txt"
@@ -6,8 +6,8 @@
         uint32 numObs = numObservations;
 
         // Get earliest observation in window
-        uint256 earliestPrice = observations[nextObsIndex];
-
+        uint32 cachednextObsIndex = nextObsIndex;
+        uint256 earliestPrice = observations[cachednextObsIndex]; 
         uint256 currentPrice = getCurrentPrice();
 
         // Calculate new moving average
@@ -18,9 +18,11 @@
         }
 
         // Push new observation into storage and store timestamp taken at
-        observations[nextObsIndex] = currentPrice;
+        observations[cachednextObsIndex] = currentPrice;
         lastObservationTime = uint48(block.timestamp);
-        nextObsIndex = (nextObsIndex + 1) % numObs;
+        unchecked {
+            nextObsIndex = (cachednextObsIndex + 1) % numObs;  
+        }
 
         emit NewObservation(block.timestamp, currentPrice, _movingAverage);
     }
```

We convert 3 storage reads to 1 storage read and 2 memory reads. Along with the unchecked operation this will save us around 250 gas.

### 2. <ins>Simplify formulas and emit local variables in `updatePrices` function</ins> 
The [updatePrices](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L158-L178) function in `RANGE.sol` can save gas by the following changes:

 1. There are four equations which follow the same kind of pattern. For example:
```solidity
movingAverage_ * (FACTOR_SCALE - wallSpread) / FACTOR_SCALE
```
This could be simplified as:
```solidity
// expanding the equation
        movingAverage_ * FACTOR_SCALE / FACTOR_SCALE - movingAverage_ * wallSpread / FACTOR_SCALE
// which is simplified into:
        movingAverage_ - movingAverage_ * wallSpread / FACTOR_SCALE
//the right hand side of the above equation is used twice so we can calculate it and save it in a memory variable. Like this:
        uint256 temp1 = movingAverage_ * wallSpread / FACTOR_SCALE;
        _range.wall.low.price = movingAverage_ - temp1;
        _range.wall.high.price = movingAverage_ + temp1;
```
 2. The emit at the end of the function uses the above storage variables. But we can save gas by just doing the calculations directly:
 
Finally the mitigation diff with the above changes looks like this:
```diff
diff --git "a/.\\orig.txt" "b/.\\modified.txt"
index 57c28a0..78909cc 100644
--- "a/.\\orig.txt"
+++ "b/.\\modified.txt"
@@ -2,20 +2,20 @@
         // Cache the spreads
         uint256 wallSpread = _range.wall.spread;
         uint256 cushionSpread = _range.cushion.spread;
-
         // Calculate new wall and cushion values from moving average and spread
-        _range.wall.low.price = (movingAverage_ * (FACTOR_SCALE - wallSpread)) / FACTOR_SCALE;
-        _range.wall.high.price = (movingAverage_ * (FACTOR_SCALE + wallSpread)) / FACTOR_SCALE;
+        uint256 temp1 = movingAverage_ * wallSpread / FACTOR_SCALE;
+
+        _range.wall.low.price = movingAverage_ - temp1;
+        _range.wall.high.price = movingAverage_ + temp1;
 
-        _range.cushion.low.price = (movingAverage_ * (FACTOR_SCALE - cushionSpread)) / FACTOR_SCALE;
-        _range.cushion.high.price =
-            (movingAverage_ * (FACTOR_SCALE + cushionSpread)) /
-            FACTOR_SCALE;
+        uint256 temp2 = movingAverage_ * cushionSpread / FACTOR_SCALE;
+        _range.cushion.low.price = movingAverage_ - temp2;
+        _range.cushion.high.price = movingAverage_ + temp2;
 
         emit PricesChanged(
-            _range.wall.low.price,
-            _range.cushion.low.price,
-            _range.cushion.high.price,
-            _range.wall.high.price
+             movingAverage_ - temp1,
+             movingAverage_ - temp2,
+             movingAverage_ + temp2,
+             movingAverage_ + temp1
         );
     }

```
The above optimization reduced the average gas consumption of `updatePrices` function from 40966 to 40605, which means a gas saving of **361**. This function is used by the [initialize](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L598), [operate](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L195)  and [setSpreads](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L498) functions in `Operator` contract as well. Which means effectively we save 3 times 361 = 1083 gas. 

### 3. <ins>Caching storage variable in the `callback` function</ins>
The [callback](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L100) function in the `BondCallback` contract reads the storage variable `ohm` multiple times. `ohm` could be cached to memory to save gas on storage reads.

The mitigation diff looks like this:
```diff
diff --git "a/.\\orig.txt" "b/.\\modified.txt"
index beb85a5..e559dc5 100644
--- "a/.\\orig.txt"
+++ "b/.\\modified.txt"
@@ -1,4 +1,4 @@
-    function callback(
+  function callback(
         uint256 id_,
         uint256 inputAmount_,
         uint256 outputAmount_
@@ -14,22 +14,22 @@
         // Check that quoteTokens were transferred prior to the call
         if (quoteToken.balanceOf(address(this)) < priorBalances[quoteToken] + inputAmount_)
             revert Callback_TokensNotReceived();
-
+        ERC20 cachedOHM = ohm;    
         // Handle payout
-        if (quoteToken == payoutToken && quoteToken == ohm) {
+        if (quoteToken == payoutToken && quoteToken == cachedOHM) { 
             // If OHM-OHM bond, only mint the difference and transfer back to teller
             uint256 toMint = outputAmount_ - inputAmount_;
             MINTR.mintOhm(address(this), toMint);
 
             // Transfer payoutTokens to sender
             payoutToken.safeTransfer(msg.sender, outputAmount_);
-        } else if (quoteToken == ohm) {
+        } else if (quoteToken == cachedOHM) {
             // If inverse bond (buying ohm), transfer payout tokens to sender
             TRSRY.withdrawReserves(msg.sender, payoutToken, outputAmount_);
 
             // Burn OHM received from sender
             MINTR.burnOhm(address(this), inputAmount_);
-        } else if (payoutToken == ohm) {
+        } else if (payoutToken == cachedOHM) {
             // Else (selling ohm), mint OHM to sender
             MINTR.mintOhm(msg.sender, outputAmount_);
         } else {

``` 

The mitigation reduced the max gas consumption of `callback` function from 187927 to 187847, which means a saving of 80 gas.

### 4. <ins>Caching storage variable in the `vote` function</ins>
The [vote](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L240-L262) function in the `Governance` contract reads the struct element `activeProposal.proposalId` multiple times. This could be cached to memory to save gas on storage reads.

The mitigation diff looks like this:
```diff
diff --git "a/.\\orig.txt" "b/.\\modified.txt"
index 6656cb4..2d24d2e 100644
--- "a/.\\orig.txt"
+++ "b/.\\modified.txt"
@@ -1,23 +1,23 @@
     function vote(bool for_) external {
         uint256 userVotes = VOTES.balanceOf(msg.sender);
-
-        if (activeProposal.proposalId == 0) {
+        uint256 cachedID = activeProposal.proposalId;
+        if (cachedID == 0) {  // @audit cache it.
             revert NoActiveProposalDetected();
         }
 
-        if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
+        if (userVotesForProposal[cachedID][msg.sender] > 0) {
             revert UserAlreadyVoted();
         }
 
         if (for_) {
-            yesVotesForProposal[activeProposal.proposalId] += userVotes;
+            yesVotesForProposal[cachedID] += userVotes;
         } else {
-            noVotesForProposal[activeProposal.proposalId] += userVotes;
+            noVotesForProposal[cachedID] += userVotes;
         }
 
-        userVotesForProposal[activeProposal.proposalId][msg.sender] = userVotes;
+        userVotesForProposal[cachedID][msg.sender] = userVotes;
 
         VOTES.transferFrom(msg.sender, address(this), userVotes);
 
-        emit WalletVoted(activeProposal.proposalId, msg.sender, for_, userVotes);
+        emit WalletVoted(cachedID, msg.sender, for_, userVotes);
     }
``` 

The mitigation reduces the max storage reads from 5 to 1. Which can save up to 400 gas.

### 5. <ins>Caching storage variable in the `executeProposal` function</ins>
The [executeProposal](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L265-L289) function in the `Governance` contract reads the struct element `activeProposal.proposalId` multiple times. This could be cached to memory to save gas on storage reads. Plus the length of the array could be cached to save gas in the `for` loop. 

The mitigation `diff` looks like this:
```diff
diff --git "a/.\\orig.txt" "b/.\\modified.txt"
index 733a8a2..41ff620 100644
--- "a/.\\orig.txt"
+++ "b/.\\modified.txt"
@@ -1,6 +1,7 @@
     function executeProposal() external {
-        uint256 netVotes = yesVotesForProposal[activeProposal.proposalId] -
-            noVotesForProposal[activeProposal.proposalId];
+        uint256 cachedID = activeProposal.proposalId;
+        uint256 netVotes = yesVotesForProposal[cachedID] -  
+            noVotesForProposal[cachedID];
         if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {
             revert NotEnoughVotesToExecute();
         }
@@ -9,16 +10,16 @@
             revert ExecutionTimelockStillActive();
         }
 
-        Instruction[] memory instructions = INSTR.getInstructions(activeProposal.proposalId);
-
-        for (uint256 step; step < instructions.length; ) {
-            kernel.executeAction(instructions[step].action, instructions[step].target);
+        Instruction[] memory instructions = INSTR.getInstructions(cachedID);
+        uint256 len = instructions.length;
+        for (uint256 step; step < len; ) {  
+            kernel.executeAction(instructions[step].action, instructions[step].target); 
             unchecked {
                 ++step;
             }
         }
 
-        emit ProposalExecuted(activeProposal.proposalId);
+        emit ProposalExecuted(cachedID);
 
         // deactivate the active proposal
         activeProposal = ActivatedProposal(0, 0);
``` 

The mitigation reduces the max storage reads from 4 to 1. Which can save up to 300 gas.

## Conclusions:

  | Issue | Gas Saved
-- | -- | -- 
1 | Caching storage variable and using `unchecked` in `updateMovingAverage()` function | 250
2 | Simplify formulas and emit local variables in `updatePrices` function | 1083 
3 | Caching storage variable in the `callback` function | 80
4 | Caching storage variable in the `vote` function | 400
5 | Caching storage variable in the `executeProposal` function | 300

### TOTAL GAS SAVED = 250 + 1083 + 80 + 400 + 300 = <ins>2113</ins>.
