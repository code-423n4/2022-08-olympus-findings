##  ++i costs less gas compared to i++
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L488
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L670
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L686

The above code lines should adopt ++i instead of i++. The post-increment (i++) operation costs more (about 5 GAS per iteration), because the compiler typically has to create a temporary variable for returning the initial value of i when incrementing i . In contrast, the pre-increment  (++i) operation simply returns the actual incremented value of i without the need for a temporary variable.  The saving impact is particularly prominent when dealing with loops. 

## Splitting if() statements that use && saves gas
Instead of using the && operator in a single if statement to check multiple conditions, using multiple if statements with 1 condition per if statement will save 3 GAS per &&. For instance, the following block of codes could be rewritten as follows:

 https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L133-L139

```
if (capacity_ < _range.high.threshold) {
    if (_range.high.active) {
        // Set wall to inactive
        _range.high.active = false;
        _range.high.lastActive = uint48(block.timestamp);

        emit WallDown(true, block.timestamp, capacity_);
    }
}
```
## External costs less gas than public visibility
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L451
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L75
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L33
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L37
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L219
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L45
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L55
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L37
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L145
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L151

The above above function not internally called may have its visibility changed to external.

##  uint256 could cost less gas  than unsigned integer types smaller than 32 bytes

The contract in PRICE.sol packs multiple types into one storage slot (lines 44 - 62). However, if you are not reading or writing all the values at the same time, this can have the opposite effect. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size to properly enforce the limits of this smaller type.

## Avoid initializing variables if they have the default value (0, false)
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L397

In the example code line above, initializing i by assigning zero to it, i = 0, is unnecessary as it has been set to zeros by default. Doing this costs extra gas. 