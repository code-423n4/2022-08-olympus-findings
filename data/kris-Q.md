## Lack of zero address check 

In contract Kernel function executeAction() missed zero address check in cases when executor or admin are changed. This may lead to breaking contract logic and making the contract not useable in the case of the executor or admin will set to zero address.

**Recommendation:** Consider adding a check if the param target_ is not the zero address in branches when the executor and admin changed.

Reference: https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L251