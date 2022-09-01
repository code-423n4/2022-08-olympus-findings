GAS

## [G-1] Using prefix increment

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L488

Using ++var than var++ can save 5 gas per exec. Or we can add unchecked (save 116 gas)
