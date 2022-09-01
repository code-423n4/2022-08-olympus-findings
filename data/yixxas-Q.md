In `executeProposal()`, if we have more `noVotes` than `yesVotes`, it means that proposal should be rejected. When a user calls the `executeProposal()` function, it will revert due to an underflow error. It would be better to catch this error and inform users who call this function the real revert reason being `noVotes > yesVotes`.

[Governance.sol#L266](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L266)
```solidity
    function executeProposal() external {
        uint256 netVotes = yesVotesForProposal[activeProposal.proposalId] -
            noVotesForProposal[activeProposal.proposalId];
        ...      
    }
```
