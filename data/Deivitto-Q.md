# QA
# Low

## transfer as reentrancy mitigation
### Summary
Fixed gas cost are not good reentrancy mitigations as the cost may change by the time.
### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L110
### Mitigation
Avoid using transfer fixed cost as a reentrancy mitigation as the gas cost may change.

## Unused return of dependents[i].configureDependencies()
### Summary
Return values checks for correct functioning, not having any check may lead to security issues.

[Kernel._reconfigurePolicies(Keycode)](src/Kernel.sol#L378-L389) ignores return value by [dependents[i].configureDependencies()](src/Kernel.sol#L383)

### Proof of Concept
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L378-L389

### Mitigation steps
Add a check for the returning value

## Missing checks for address(0x0) on executeAction
### Summary
Zero address should be checked for some function parameters. For example in functions like mints, withdrawals... 

A zero address can lead into serious problems as locking eth or correct functioning.

### Details (CHECK)
The [FUNCTION]  function is public and uses an address parameter, this means it can be called from wherever, so using an incorrect address can be done.

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L235
### Mitigation
Check zero address before assigning or using it


# Informational
##  Use of hardcoded values is confusing and risky
### Summary:
Hardcoded values are used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details:
Values are hardcoded and would be more readable and maintainable if declared as a constant. Hardcoding values can lead to typos that generate serious problems.

### Github Permalinks
0x41, 0x5A
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/utils/KernelUtils.sol#L46
```
       if (char < 0x41 || char > 0x5A) revert InvalidKeycode(keycode_); // A-Z only
```
0x61, 0x7A
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/utils/KernelUtils.sol#L60
```
      if ((char < 0x61 || char > 0x7A) && char != 0x5f && char != 0x00) {
```

7 days, 10_000, 1 hours, 10000, 100
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L103-L111
```
      if (configParams[1] > uint256(7 days) || configParams[1] < uint256(1 days))
            revert Operator_InvalidParams();

        if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();

        if (configParams[3] < uint32(1 hours) || configParams[3] > configParams[1])
            revert Operator_InvalidParams();

        if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();

```

"PRICE", "RANGE", "TRSRY", "MINTR"
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L156-L159
```
        dependencies[0] = toKeycode("PRICE");
        dependencies[1] = toKeycode("RANGE");
        dependencies[2] = toKeycode("TRSRY");
        dependencies[3] = toKeycode("MINTR");
```

100, 10000
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L245-L248
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L264
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L164
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L217
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L268
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L111
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L106

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L121
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L106
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L518
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L535
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L550

36
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L378
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L433
### Mitigation
Replace hardcoded values with declared constants.
Comment what these values are intended for

## Naming convention of constants
### Summary:
 Constant naming convention is all upper case. 
### Details:
Some constants are not using proper style.
Constant should be in UPPER_CASE_WITH_UNDERSCORES as per Solidity Style Guide. 
### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L59
`uint8 public constant decimals = 18;`

### Mitigation
 Rename the constant to uppercase style: CONSTANTS_WITH_UNDERSCORES. 

## Naming convention of state variable non constant
### Summary
  Only constants are suggested to use style CONSTANTS_WITH_UNDERSCORES, other variables are suggested to use camelCase
### Details
Different variables are using CONSTANTS_WITH_UNDERSCORES style even if they are not constant. This may lead to wrong assumptions
  ```
    OlympusPrice internal PRICE;
    OlympusRange internal RANGE;
    OlympusTreasury internal TRSRY;
    OlympusMinter internal MINTR;
    OlympusInstructions public INSTR;
    OlympusVotes public VOTES;
  ```

Happens the same with some functions
```
    function KEYCODE() public pure override returns (Keycode) {
        return toKeycode("TRSRY");
    }

    function VERSION() external pure override returns (uint8 major, uint8 minor) {
        return (1, 0);
    }
    function INIT() external virtual onlyKernel {}
```
### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/TreasuryCustodian.sol#L20
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L69-L72
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/BondCallback.sol#L29-L30
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Heart.sol#L45
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L11
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L56-L57
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/VoterRegistration.sol#L10

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L47-L53
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/VOTES.sol#L22-L29
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol#L23-L30
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L94-L105
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/MINTR.sol#L19-L27
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L107-L115
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L109-L117
### Mitigation
Rename to camelCase

## Missing indexed event parameters
### Summary:
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details:
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol#L11
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L26-L28
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L19-L31
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L86-L90
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Heart.sol#L28-L30
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L51-L54

### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.

## Unrecheable code because of revert
### Impact
Unreachable code affects clarity of the code and gas usage at deployment
### Github Permalink
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/VOTES.sol#L46-L47
warning[5740]: Warning: Unreachable code.
  --> src/modules/VOTES.sol:47:9:
   |         revert VOTES_TransferDisabled();
47 |         return true;
   |         ^^^^^^^^^^^
### Mitigation
Consider removing Unreachable code.

## Unused code
### Summary
Code that is not used should be removed
### Details:
- variable to_ and variable amount_ are declared but not used, this leads to warnings while compiling and more gas usage as more variables are used.
- some other code is not being used in Kernel and KernelUtils.

### Github Permalinks
warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
  --> src/modules/VOTES.sol:45:23:
   |
45 |     function transfer(address to_, uint256 amount_) public pure override returns (bool) {
   |                       ^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
  --> src/modules/VOTES.sol:45:36:
   |
45 |     function transfer(address to_, uint256 amount_) public pure override returns (bool) {
   |                                    ^^^^^^^^^^^^^^^

[Policy.getModuleAddress(Keycode)](src/Kernel.sol#L131-L135) is never used and should be removed

src/Kernel.sol#L131-L135

[toKeycode(bytes5)](src/utils/KernelUtils.sol#L11-L13) is never used and should be removed

src/utils/KernelUtils.sol#L11-L13

[fromKeycode(Keycode)](src/utils/KernelUtils.sol#L16-L18) is never used and should be removed

src/utils/KernelUtils.sol#L16-L18

[fromRole(Role)](src/utils/KernelUtils.sol#L26-L28) is never used and should be removed

src/utils/KernelUtils.sol#L26-L28

[toRole(bytes32)](src/utils/KernelUtils.sol#L21-L23) is never used and should be removed

src/utils/KernelUtils.sol#L21-L23

### Mitigation
Remove the code that is not used.


## Missing Natspec 
 ### Summary: 
 Missing Natspec and regular comments affect readability and maintainability of a codebase. 
 ### Details: 
 Contracts has partial or full lack of comments
 ### Github Permalinks 
 - Lack of multiple natspec and comments
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/VOTES.sol
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/MINTR.sol
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L61-L80
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L18-L35
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Heart.sol
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/BondCallback.sol
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/TreasuryCustodian.sol
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/utils/KernelUtils.sol

 - Partial lack of comments / some natspect properties
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol

 ### mitigation
 Add @param descriptors
 Add @return descriptors
 Add Natspec comments. 
 Add inline comments. 
 Add comments for what the contract does






## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
As Solidity guidelines says about declaration order:
1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions
Also, state variables order affects to gas in the same way as ordering structs for saving storage slots

### github permalink
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L55-L137

### Mitigation
Follow solidity style guidelines https://docs.soliditylang.org/en/v0.8.15/style-guide.html



## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity >=0.8.0. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IHeart.sol#L2
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L2
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondCallback.sol#L2

### Mitigation (CHECK)
Lock pragmas to a specific Solidity version. 
Consider converting >= 0.8.0 into 0.8.15

## Maximum line length exceeded
### Summary:
 Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details: 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks: 

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L108

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L147

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L148

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L154

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L173

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L234

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L348

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L438

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L450

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol#L54

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol#L62

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol#L63

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol#L64

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol#L65

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L18

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L19

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L20

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L21

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L31

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L39

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L40

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L43

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L46

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L78

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L120

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L121

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L164

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L189

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L201

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L202

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L203

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L204

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L236

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L241

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L262

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L263

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L264

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L265

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L267

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L12

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L13

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L14

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L40

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L44

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L46

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L47

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L48

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L61

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L62

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L97

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L125

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L126

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L132

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L144

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L167

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L182

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L183

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L212

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L214

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L239

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L240

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L241

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L261

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L262

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L280

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L290

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L301

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L329

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L339

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L121

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/BondCallback.sol#L91

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L104

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L113

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L119

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L126

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L156

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L157

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L158

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Heart.sol#L18

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Heart.sol#L19

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L23

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L24

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L25

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L26

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L27

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L28

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L29

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L97

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L199

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L237

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L243

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L362

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L374

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L378

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L429

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L433

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L443

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L472

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L481

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L491

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L492

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L632

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L633

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L657

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L730

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L734

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L41

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L43

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L44

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L53

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L54

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L65

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L66

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L67

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L68

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IHeart.sol#L10

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IHeart.sol#L11

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L13

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L15

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L16

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L17

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L19

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L29

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L34

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L72

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L73

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L79

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L84

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L90

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L91

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L100

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L106

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L108

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L124

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L130

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L135

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L141

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAggregator.sol#L29

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAggregator.sol#L41

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAggregator.sol#L42

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAggregator.sol#L52

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAggregator.sol#L53

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAggregator.sol#L79

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAggregator.sol#L84

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAggregator.sol#L90

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L12

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L14

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L15

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L16

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L17

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L18

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L19

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L21

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L22

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L26

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L31

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L32

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L33

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L34

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L35

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L36

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L37

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L40

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L41

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L42

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L72

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L83

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L88

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L89

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L101

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L109

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L110

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L111

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L112

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L113

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L114

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L119

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L123

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L125

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L132

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L134

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L164

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L165

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L175

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L176

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondCallback.sol#L7

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondCallback.sol#L13

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondCallback.sol#L20

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondTeller.sol#L9

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondTeller.sol#L15

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondTeller.sol#L24

### Mitigation
Reduce line length to less than 99 at least to improve maintainability and readability of the code 

## Large multiples of ten should use scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability
### Summary:
Multiples of 10 can be declared as constants with scientific notation so it's easier to read them and less prone to miss/exceed a 0 of the expected value.

### Details
Values 10000 and 100 can be used in scientific notation
### Github Permalinks
10000, 100
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L245-L248
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L264
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L164
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L217
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L268
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L111
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L106

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L121
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L106
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L518
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L535
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L550
### Mitigation
Replace hardcoded numbers with constants that represent the scientific corresponding notation

## Open TODOs   
### Summary
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
### Details
The code includes a TODO already done that affects readability and focus on the readers/auditors of the contracts
### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L657
        /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/TreasuryCustodian.sol#L51
    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/TreasuryCustodian.sol#L52
    // TODO must reorg policy storage to be able to check for deactivated policies.

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/TreasuryCustodian.sol#L56
        // TODO Make sure `policy_` is an actual policy and not a random address.



### Mitigation
Remove already done TODO




