 # Low Risk Issues

## Summary Of Findings:

  | Issue 
-- | -- 
1 | Zero-check is not performed for address
2 | Check if Outstanding Debt is less than `_amount` in `repayLoan` function

## Details on Findings:

### 1. <ins>Zero-check is not performed for address</ins>

In `Kernel.sol`, address is not checked for a zero value before assigning it to `kernel` in the [changeKernel](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L76) function.

Mitigation would be to add an `if` statement in the function as shown below:

```solidity
    function changeKernel(Kernel newKernel_) external onlyKernel {
        if(address(newKernel_) != address(0)) 
            kernel = newKernel_;
    }
```

### 2. <ins>Check if Outstanding Debt is less than `_amount` in `repayLoan` function</ins>

In `TRSRY.sol`, in the [repayLoan](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L106) function, we first check if there is any outstanding debt for the `msg.sender`. I recommend to modify this, so that we also check that the outstanding debt is less than `_amount`, which the user is trying to repay. 

It's not a security issue as the function will revert because of this line: `reserveDebt[token_][msg.sender] -= received;`

Mitigation would be like this:
```solidity
// Change this:
    if (reserveDebt[token_][msg.sender] == 0) revert TRSRY_NoDebtOutstanding();
// into:
    if (reserveDebt[token_][msg.sender] < _amount || reserveDebt[token_][msg.sender] == 0) revert TRSRY_NotEnoughDebtOutstanding();
```

 # Non-critical Issues

## Summary Of Findings:

  | Issue 
-- | --  
1 | Custom errors could be used with parameters for better user experience
2 | Open TODO


## Details on Findings:

### 1. <ins>Custom errors could be used with parameters for better user experience</ins>
The following custom parameters could use some parameters to show what actually went wrong.

 1. [RANGE_InvalidParams()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L250)
 2. [TRSRY_NotApproved()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L144)
 3. [revert NotEnoughVotesToPropose()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L165)
 4. [SubmittedProposalHasExpired()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L213)
 5. [NotEnoughEndorsementsToActivateProposal()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L220)
 6. [ActiveProposalNotExpired();](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L228)
 7. [NotEnoughVotesToExecute()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L269)
 8. [ExecutionTimelockStillActive()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L273)
 9. [Heart_OutOfCycle()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L94)
 10. [Operator_InsufficientCapacity()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L758)
 11. [Operator_InvalidParams()](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol)

### 2. <ins>Open TODO</ins>
There is one instance of this in [Operator.sol](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L657)
