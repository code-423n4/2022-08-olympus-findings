1. Useage of uint / int smaller than 32 bytes incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

POC : 

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L83
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L86
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L485