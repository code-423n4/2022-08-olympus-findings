# Initailizing variable that are by default initialized causes gas fees

In the *for* loop in the function `_setPolicyPermissions`, the iterator `i` is initialized to 0, when by default it is zero
(https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol)

Similarly, in KernelUtils.sol also this happpens
(https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L40)
(https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L55)