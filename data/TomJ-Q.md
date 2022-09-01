## Table of Contents
### Low Risk Issues
- Missing Zero Address Check 
- safeApprove is Deprecated
- Admin Address Change should be a 2-Step Process
&ensp;
## Low Risk Issues
### Missing Zero Address Check 

#### Issue
I recommend adding check of 0-address for input validation of critical address parameters.
Not doing so might lead to non-functional contract and have to redeploy the contract, when it is updated to 0-address accidentally.

#### PoC
Total of 14 instances found.

1. kernel address of constructor() of KernelAdapter
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L65-L66

2. kernel address of changeKernel() of KernelAdapter
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L76-L77

3. executor address of executeAction() of Kernel
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L250-L251

4. admin address of executeAction() of Kernel
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L252-L253
    
5. _ohmEthPriceFeed address of constructor() of OlympusPrice
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L83

6. _reserveEthPriceFeed address of constructor() of OlympusPrice
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L86

7. ohm address of constructor() of Operator
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L121

8. reserve address of constructor() of Operator
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L123

9. ohm address of constructor() of BondCallback
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L44

10. aggregator address of constructor() of BondCallback
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L43

11. _operator address of constructor() of OlympusHeart
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L60

12. rewardToken address of constructor() of OlympusHeart
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L64

13. ohm address of constructor() of OlympusRange
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L102

14. reserve address of constructor() of OlympusRange
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L103

#### Mitigation
Add 0-address check for above addresses.

&ensp;
### safeApprove is Deprecated

#### Issue
safeApprove is deprecated.
Please use safeIncreaseAllowance and safeDecreaseAllowance instead.
link: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45

#### PoC
Total of 2 instances found.
```
./Operator.sol:167:        ohm.safeApprove(address(MINTR), type(uint256).max);
./BondCallback.sol:57:        ohm.safeApprove(address(MINTR), type(uint256).max);
```

#### Mitigation
Please use safeIncreaseAllowance and safeDecreaseAllowance instead.

&ensp;
### Admin Address Change should be a 2-Step Process 

#### Issue
High privilege account such as admin / owner is changed with only single process.
This can be a concern since an admin / owner role has a high privilege in the contract and
mistakenly setting a new admin to an incorrect address will end up losing that privilege.

#### PoC
Total of 4 instances found.

1. "kernel" privilege of KernelAdapter 
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L76-L77

2. "executor" privilege of Kernel
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L250-L251

3. "admin" privilege of Kernel
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L252-L253

4. "operator" privilege of BondCallback
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L190-L193

#### Mitigation
This can be fixed by implementing 2-step process. We can do this by following. 
First make the setAdmin function approve a new address as a pending admin.
Next that pending admin has to claim the ownership in a separate transaction to be a new admin.

&ensp;