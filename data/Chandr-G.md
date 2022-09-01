
# PREFIX INCREMENTS


IMPACT
  Prefix increments are cheaper than postfix increments.
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol#L49
   i++;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol#L64
   i++;

 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L488
   decimals++;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L670
   _status.low.count++;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L686
   _status.high.count++;

Mitigation:
  replace foo++ to ++foo


 # DEFAULT VALUE INITIALIZATION

 IMPACT
   If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L397
   for (uint256 i = 0; i < reqLength; ) {

 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol#L43
   for (uint256 i = 0; i < 5; ) {
     
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol#L58
   for (uint256 i = 0; i < 32; ) {
     
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L574
   _status.high.count = 0;
     
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L575
   _status.high.nextObservation = 0;
     
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L578
   _status.low.count = 0;
     
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L579
   _status.low.nextObservation = 0;

 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L254
   _movingAverage = 0;

 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L286
   _movingAverage = 0;
     
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L135
   _range.high.active = false;
     
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L147
   _range.low.active = false;
     

Mitigation:
  Remove explicit value initialization.
 

 # COMPARISON OPERATORS

 IMPACT
   In the EVM, there is no opcode for >= or <=. When using greater than or equal, two operations are performed: > and =.
   Using strict comparison operators hence saves gas.
   
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L210
   uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L211
   _status.high.count >= config_.regenThreshold
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L216
   uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L217
   _status.low.count >= config_.regenThreshold
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L486
   while (price_ >= 10) {
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L667
   if (currentPrice >= movingAverage) {
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L683
   if (currentPrice <= movingAverage) {
 

Mitigation:
  Replace <= with <, and >= with >. Do not forget to increment/decrement the compared variable
 


 
 # COMPARISON WITH ZERO

 IMPACT
   >0 is less gas efficient than !0 if you enable the optimizer at 10k AND you’re in a require statement. Detailed explanation with the opcodes [here](https://twitter.com/gzeon/status/1485428085885640706)

 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L247
   if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
 

Mitigation:
   Replace >0 with !0
 

# Increment/decrement operations

 IMPACT
    X = X + Y IS CHEAPER THAN X += Y
    X = X- Y IS CHEAPER THAN X -= Y

 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L194
   totalEndorsementsForProposal[proposalId_] -= previousEndorsement;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L198
   totalEndorsementsForProposal[proposalId_] += userVotes;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L252
   yesVotesForProposal[activeProposal.proposalId] += userVotes;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L254
   noVotesForProposal[activeProposal.proposalId] += userVotes;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L103
   lastBeat += frequency();
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L143
   _amountsPerMarket[id_][0] += inputAmount_;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L144
   _amountsPerMarket[id_][1] += outputAmount_;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L96
   reserveDebt[token_][msg.sender] += amount_;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L97
   totalDebt[token_] += amount_;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L115
   reserveDebt[token_][msg.sender] -= received;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L116
   totalDebt[token_] -= received;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L131
   if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L132
   else totalDebt[token_] -= oldDebt - amount_;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L56
   balanceOf[from_] -= amount_;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L58
   balanceOf[to_] += amount_;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L136
   _movingAverage += (currentPrice - earliestPrice) / numObs;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L138
   _movingAverage -= (earliestPrice - currentPrice) / numObs;
 
 Instance:
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L222
   total += startObservations_[i];
 

Mitigation:
   X += Y replace with X = X + Y
   X -= Y replace with X = X - Y
 


