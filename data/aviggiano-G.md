Gas optimization in `src/policies/Governance.sol` function `endorseProposal(uint256 proposalId_)`

[Before](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L194)
```
        // undo any previous endorsement the user made on these instructions
        uint256 previousEndorsement = userEndorsementsForProposal[proposalId_][msg.sender];
        totalEndorsementsForProposal[proposalId_] -= previousEndorsement;

        // reapply user endorsements with most up-to-date votes
        userEndorsementsForProposal[proposalId_][msg.sender] = userVotes;
        totalEndorsementsForProposal[proposalId_] += userVotes;

```
```
// gas reporter
│ endorseProposal                                        ┆ 6874            ┆ 39015  ┆ 30774  ┆ 52674  ┆ 43      │
```

After

```
        // undo any previous endorsement the user made on these instructions
        uint256 previousEndorsement = userEndorsementsForProposal[proposalId_][msg.sender];

        // reapply user endorsements with most up-to-date votes
        userEndorsementsForProposal[proposalId_][msg.sender] = userVotes;
        totalEndorsementsForProposal[proposalId_] += userVotes - previousEndorsement;
```

```
// gas reporter
│ endorseProposal                                        ┆ 6449            ┆ 38610  ┆ 30349  ┆ 52249  ┆ 43      │
```