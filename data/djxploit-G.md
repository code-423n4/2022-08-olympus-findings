## Unnecessary initialization
In line https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L397, initialization of i to 0 is not necessary, as the default value of uint256 is always 0.

## For loop optimization
In loop , https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L43, the for loop can be optimized by removing unnecessary initialization and using ++i instead of i++ 
```
for(uint256 i; i<5;) {
...
unchecked {
++i;
}
}
```
Similar optimizations can also be done at :
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L58


## Use >= or <= instead of > or < :
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L46
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L60
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L144
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L133
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L145
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L245
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L247
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L248
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L249
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L264
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L90
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L135


