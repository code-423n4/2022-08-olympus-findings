1.	Check if `instructionsId_` exists. Better revert with message then return zeroes.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L38
2.	Check address for 0 to not burn tokens.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L34
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L36
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L58
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L47
3.	FullMath lib is not used. Remove dependency.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L9
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L23
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L5
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L18
4.	Check address params if not 0 before setting.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L102-L103
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L144
5.	Check if `proposalId_` exists. Better revert with message then return zeroes.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L146
6.	Check if `instructions_` is empty on top of the function. Save user’s gas.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L160
7.	Better make check if ` yesVotesForProposal[activeProposal.proposalId]` is bigger then ` noVotesForProposal[activeProposal.proposalId]` and revert special error, than do underflow error.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L266
8.	Make `KERNEL::configureDependencies` function use modifier `onlyKernel` as this should be called by kernel.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L139
