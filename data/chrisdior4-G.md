## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES  

There are 14  instances of this issue:
 ================= 
file: OlympusERC20.sol  

1.counter._value += 1;

1.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/external/OlympusERC20.sol#L651   

Should be:  

1.counter._value = counter._value + 1;  

/////////////////////////////  

file:PRICE.sol

  2._movingAverage += (currentPrice - earliestPrice) / numObs;  
 3._movingAverage -= (earliestPrice - currentPrice) / numObs; 
 4. total += startObservations_[i];

   2. https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L136 3. 3.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L138 4. 4.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L222  

Should be:  

2. _movingAverage = _movingAverage  + (currentPrice - earliestPrice) / numObs; 
3._movingAverage = _movingAverage  - (earliestPrice - currentPrice) / numObs; 
4.  total = total + startObservations_[i];


 /////////////////////  

File: TRSRY.sol  

5.reserveDebt[token_][msg.sender] += amount_;
 6.totalDebt[token_] += amount_;  

5.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L96 
6. https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L96  

Should be:  

5.reserveDebt[token_][msg.sender] = reserveDebt[token_][msg.sender] + amount_; 
6.totalDebt[token_] = totalDebt[token_] +  amount_;  

//////////////////

  File: Votes.sol  

7.balanceOf[from_] -= amount_; 
8.balanceOf[to_] += amount_;  

7.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L56 8.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L58  

Should be:  

7.balanceOf[from_] = balanceOf[from_] -  amount_; 
8.balanceOf[to_] = balanceOf[to_] + amount_;  

///////////////////

file: BondCallback.sol  

9. _amountsPerMarket[id_][0] += inputAmount_; 
10._amountsPerMarket[id_][1] += outputAmount_;  

9.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L143 10.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L144  

Should be:  

9. _amountsPerMarket[id_][0] = _amountsPerMarket[id_][0] +  inputAmount_; 
10._amountsPerMarket[id_][1] =  _amountsPerMarket[id_][1] + outputAmount_; 


/////////////////// 

File: Governance.sol  

11.totalEndorsementsForProposal[proposalId_] += userVotes; 
12.yesVotesForProposal[activeProposal.proposalId] += userVotes; 
13.noVotesForProposal[activeProposal.proposalId] += userVotes;

11.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L198 12.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L198 13.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L254

Should be:  

11.totalEndorsementsForProposal[proposalId_] = totalEndorsementsForProposal[proposalId_]  + userVotes; 12.yesVotesForProposal[activeProposal.proposalId] = yesVotesForProposal[activeProposal.proposalId] +  userVotes; 13.noVotesForProposal[activeProposal.proposalId] = noVotesForProposal[activeProposal.proposalId] + userVotes; 

/////////////  

File:Heart.sol  

14.lastBeat += frequency();  

14.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L103  

Should be:  

14.lastBeat = lastBeat + frequency();


 =================  
## USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

 There are 8 instances of this issue:
 ========================= 

File: PRICE.sol


1.uint256[] memory startObservations_  

1.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L205  

Should be:  

1.uint256[] storage startObservations_  

/////////////////////////////  

File:RANGE.sol  

2.ERC20[2] memory tokens_, 
3.uint256[3] memory rangeParams_   

2. https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L79 
3. https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L80    

Should be:

   2. ERC20[2] storage tokens_, 
 3. uint256[3] storage rangeParams_   

///////////////////  

File:BondCallback.sol  

4.uint256[2] memory marketAmounts = _amountsPerMarket[id_];  

4.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L179  

Should be:   

4.uint256[2] storage marketAmounts = _amountsPerMarket[id_];

   ////////////////  

File: Governance.sol

  5.Instruction[] memory instructions = INSTR.getInstructions(proposalId_);  

5.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L187  

5.Instruction[] storage instructions = INSTR.getInstructions(proposalId_);

    ///////////////////  

File:Operator.sol  

6.ERC20[2] memory tokens_,
 7.uint32[8] memory configParams
 8.Regen memory regen = _status.low;  

6.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L96 7.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L97 8.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L666  

Should be:  

6.ERC20[2] storage tokens_, 
7.uint32[8] storage configParams 
8.Regen storage regen = _status.low; 


======================== 
## ++i  COSTS LESS GAS THAN i++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS
========================    

  There are 4 instances of this issue:
  ====================== 
file: Operator  

1. decimals++; 
2._status.low.count++; 
3._status.low.count++;


  1.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L488 2.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L670 3.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L670
 
Should be:  

1. ++decimals; 
2.++_status.low.count; 
3.++_status.low.count; 

///////////////////  

File:Deploy.sol  

4.for (uint i = 0; i < 90; i++) {  

4.https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/scripts/Deploy.sol#L239

  Should be:  

4.for (uint i = 0; i < 90; ++i) {  
=================== 