## SUMMARY

### [L-01] REPLACE INLINE ASSEMBLY WITH `ACCOUNT.CODE.LENGTH`
`<address>.code.length` can be used in Solidity >= 0.8.0 to access an account's code size and check if it is a contract without inline assembly.

*There is 1 instance of this issue:*

[KernalUtils.sol#ensureContract](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L33-L35)

```
File: src/utils/KernelUtils.sol

31   function ensureContract(address target_) view {
32      uint256 size;
33      assembly {
34          size := extcodesize(target_)
35      }
36      if (size == 0) revert TargetNotAContract(target_);
37:  }
```

### [N-01] INCORRECT COMMENT
Returns a bool flag for whether the tokens have been claimed, not the amount of tokens reclaimed.

*There is 1 instance of this issue:*

[Governance.sol](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L116)

```
File: src/policies/Governance.sol

116:   /// @notice Return the amount of tokens reclaimed by a user after voting on a proposal id.
```

### [N-02] SAFEAPPROVE() IS DEPRECATED
`safeApprove()` is [deprecated](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45) in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. 

*There are 2 instance(s) of this issue:*

[Operator.sol#configureDependencies](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L167)

```
File: src/policies/Operator.sol    #1

167:   ohm.safeApprove(address(MINTR), type(uint256).max);
```
[BondCallback.sol#configureDependencies](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L57)

```
File: src/policies/BondCallback.sol    #2

57:   ohm.safeApprove(address(MINTR), type(uint256).max);
```
### [N-03] OPEN TODOS
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

*There are 3 instance(s) of this issue:*

[Operator.sol#_addObservation](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L657)

```
File: src/policies/Operator.sol    #1

657:   /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
```
[TreasuryCustodian.sol](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L51-L52)

```
File: src/policies/TreasuryCustodian.sol    #2

51    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
52:   // TODO must reorg policy storage to be able to check for deactivated policies.
```
[TreasuryCustodian.sol#revokePolicyApprovals](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L56)

```
File: src/policies/TreasuryCustodian.sol    #3

56:   // TODO Make sure `policy_` is an actual policy and not a random address.
```