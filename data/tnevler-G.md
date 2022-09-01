# Report
## Gas Optimizations ##

### [G-01]: Use new variable instead of reading array length in every loop of a for-loop ###
**Context:**

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L278


**Description:**

If you read the length of the array at each iteration of the loop, this consumes a lot of gas.


**Recommendation:**

Store the array’s length in a variable before the for-loop, and use this new variable in the loop.


### [G-02]: variable can be immutable ### 
**Context:**

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L32


**Description:**

Variable is set in the constructor and never modified after that.

**Recommendation:**

It is more gas efficient to mark it as immutable.


### [G-03]: X += Y costs more gas than X = X + Y ###
**Context:** 

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L96

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L97
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L115
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L116
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L132
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L222
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L138
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L58
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L56
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L144
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L198
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L252

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L254
  
+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L194

**Recommendation:**

Change X += Y (X -= Y) to X = X + Y (X = X - Y).


### [G-04]: i++ costs more gas than ++i ###
**Context:** 

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L488

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L670

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L686

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L691


**Recommendation:**

Change i++ (i--) to ++i (--i).

### [G-05]: Don't initialize variable with its default value ###
**Context:** 

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L397

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L43

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L58

**Description:**

Default value of uint is 0. It's unnecessary and costs more gas to initialize uint variavles to 0.

**Recommendation:**

Change uint256 i = 0; to uint256 i;


### [G-06]: >0 costs more gas than !=0 ###
**Context:** 

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L247

**Description:**

uint256 is a unsigned integer. 

userVotesForProposal[activeProposal.proposalId][msg.sender] will never be less than 0.

**Recommendation:**

Change to 
```
if (userVotesForProposal[activeProposal.proposalId][msg.sender] != 0) {
```