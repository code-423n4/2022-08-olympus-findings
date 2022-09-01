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


