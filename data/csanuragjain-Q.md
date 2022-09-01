## Activated proposal can still be endorsed

Contract:
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L180

Function:
endorseProposal

Issue:
It was observed that even though a proposal is activated, user can still endorse it

Recommendation:
Do not allow endorsing once proposal is activated

## Zero address check missing

Contract:
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L76

Function:
changeKernel

Issue:
It was observed that new kernel can be set as zero address which will prohibit all kernel activities

Recommendation:
Do not allow zero address while setting kernel