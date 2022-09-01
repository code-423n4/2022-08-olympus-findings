### Calldata variables don't need to be cached as their access cost is the same as local variables

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L43
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L418

### Variables can be declared before the loop instead of inside the loop to avoid declaration costs

https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L44
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L59
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L418
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L307
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L363

