# Gas Optimizations Report for Olympus DAO contest

## Overview
During the audit, 6 gas issues were found.

№ | Title | Instance Count
--- | --- | --- 
G-1 | [Postfix increment and decrement](#g-1-postfix-increment-and-decrement) | 5
G-2 | [<>.length in loops](#g-2-length-in-loops) | 1
G-3 | [Initializing variables with default value](#g-3-initializing-variables-with-default-value) | 3
G-4 | [Some variables can be immutable](#g-4-some-variables-can-be-immutable) | 1
G-5 | [> 0 is more expensive than =! 0](#g-5--0-is--more-expensive-than--0) | 1
G-6 | [x += y is more expensive than x = x + y](#g-6-x--y-is--more-expensive-than-x--x--y) | 18

## Gas Optimizations Findings (6)
### G-1. Postfix increment and decrement
##### Description
Prefix increment and decrement cost less gas than postfix.

##### Instances
- [```decimals++;```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L488)
- [```_status.low.count++;```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L670)
- [```_status.high.count++;```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L686)
- [```_status.low.count--;```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L675)
- [```_status.high.count--;```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L691)

##### Recommendation
Consider using prefix increment and decrement  where it is relevant. 

#
### G-2. <>.length in loops
##### Description
Reading the length of an array at each iteration of the loop consumes extra gas.

##### Instances
[```for (uint256 step; step < instructions.length; ) {```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L278)

##### Recommendation
Store the length of an array in a variable before the loop, and use it.

#
### G-3. Initializing variables with default value
##### Description
It costs gas to initialize integer variables with 0 or bool variables with false but it is not necessary.

##### Instances
- [```for (uint256 i = 0; i < reqLength; ) {```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L397)
- [```for (uint256 i = 0; i < 5; ) {```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L43)
- [```for (uint256 i = 0; i < 32; ) {```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L58)

##### Recommendation
Remove initialization for default values.  
For example:
```for (uint256 i; i < reqLength; ) {```

#
### G-4. Some variables can be immutable
##### Description
Using immutables is cheaper than storage-writing operations.

##### Instances
[```ERC20 public ohm;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L32)

##### Recommendation
Use immutables where possible.
Change to ``` ERC20 public immutable ohm;```

#
### G-5. ```> 0``` is  more expensive than ```=! 0```
##### Instances
[```if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L247)

##### Recommendation
Use ```=! 0``` instead of ```> 0```, where possible.

#
### G-6. ```x += y``` is  more expensive than ```x = x + y```
##### Instances
- [```reserveDebt[token_][msg.sender] += amount_;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L96)
- [```totalDebt[token_] += amount_;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L97)
- [```reserveDebt[token_][msg.sender] -= received;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L115)
- [```totalDebt[token_] -= received;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L116)
- [```if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131)
- [```else totalDebt[token_] -= oldDebt - amount_;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L132)
- [```_movingAverage += (currentPrice - earliestPrice) / numObs;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136)
- [```_movingAverage -= (earliestPrice - currentPrice) / numObs;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L138)
- [```total += startObservations_[i];```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L222)
- [```balanceOf[from_] -= amount_;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L56)
- [```balanceOf[to_] += amount_;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L58)
- [```_amountsPerMarket[id_][0] += inputAmount_;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143)
- [```_amountsPerMarket[id_][1] += outputAmount_;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L144)
- [```lastBeat += frequency();```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103)
- [```totalEndorsementsForProposal[proposalId_] -= previousEndorsement;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L194)
- [```totalEndorsementsForProposal[proposalId_] += userVotes;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L198)
- [```yesVotesForProposal[activeProposal.proposalId] += userVotes;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L252)
- [```noVotesForProposal[activeProposal.proposalId] += userVotes;```](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L254)

##### Recommendation
Use ```x = x + y``` instead of ```x += y```.
Use ```x = x - y``` instead of ```x -= y```.