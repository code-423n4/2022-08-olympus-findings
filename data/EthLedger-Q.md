Low Risk Findings:

1. Missing checks for address(0)

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L251
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L253

2. Unused internal functions:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L131
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L16
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L26
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L11
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L21

3. Use of block.timestamp()

Instead of using block.timestamp(), It is recommended to use block.number plus an average block time to estimate times, as this is less easily manipulated by miners:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L165
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L171
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L215

Non-Critical Findings:

1. Use only uppercase letters for constant variables

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59

2. Public functions not used internally should be declared external:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L451

3. Transfer function in VOTES.sol could be removed

Since transfers are locked for this token, this function could be removed:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L45
