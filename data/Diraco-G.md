1 Use != 0 instead of > 0 at the above mentioned codes. The variable is uint, so it will not be below 0 so it can just check != 0.
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L247

2   ++I COSTS LESS GAS COMPARED TO I++ 

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L670
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L686