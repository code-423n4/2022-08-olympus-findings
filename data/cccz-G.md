## [G-01] OlympusPrice.constructor(): SHOULD USE MEMORY INSTEAD OF STORAGE VARIABLE

See @audit tag

```
        _ohmEthPriceFeed = ohmEthPriceFeed_;
        uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals(); // @audit gas: should use ohmEthPriceFeed_.decimals()

        _reserveEthPriceFeed = reserveEthPriceFeed_;
        uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals(); // @audit gas: should use reserveEthPriceFeed_.decimals()
```

## [G-02] OlympusPrice.updateMovingAverage(): L136 AND L138 SHOULD BE UNCHECKED DUE TO L135

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block: https://docs.soliditylang.org/en/v0.8.7/control-structures.html#checked-or-unchecked-arithmetic


```
135:        if (currentPrice > earliestPrice) {
136:            _movingAverage += (currentPrice - earliestPrice) / numObs;
137:        } else {
138:            _movingAverage -= (earliestPrice - currentPrice) / numObs;
139:        }
```

## [G-03] OlympusGovernance.executeProposal : kernel SHOULD GET CACHED

See @audit tag

```
        for (uint256 step; step < instructions.length; ) {
            kernel.executeAction(instructions[step].action, instructions[step].target); // @audit gas: should cache "kernel" (SLOAD IN LOOP)
            unchecked {
                ++step;
            }
        }
```

## [G-04] OlympusGovernance.vote : > 0 IS LESS EFFICIENT THAN != 0 FOR UNSIGNED INTEGERS 

!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)

```
    function vote(bool for_) external {
        uint256 userVotes = VOTES.balanceOf(msg.sender);

        if (activeProposal.proposalId == 0) {
            revert NoActiveProposalDetected();
        }

        if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) { // @audit gas: should use userVotesForProposal[activeProposal.proposalId][msg.sender] != 0
            revert UserAlreadyVoted();
        }
```

##  [G-05] TreasuryCustodian.revokePolicyApprovals : TRSRY SHOULD GET CACHED

See @audit tag

```
        for (uint256 j; j < len; ) {
            TRSRY.setApprovalFor(policy_, tokens_[j], 0); // @audit gas: should cache "TRSRY" (SLOAD IN LOOP)
            unchecked {
                ++j;
            }
        }
```