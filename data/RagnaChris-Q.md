1. Consider two-phase executor role transfer
There are risk that the executor role is transferred to the wrong or invalid address, thus causing the thus without a owner.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L250-L251

Recommendation
Consider implementing a two step process where the executor nominates an account and the nominated account needs to call a function to accept the role transfer for the transfer of executor to fully succeed.