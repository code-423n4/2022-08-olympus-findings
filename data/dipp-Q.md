# [L-01] Incorrect ```lastBeat``` if ```resetBeat``` is called

## Line References

[Heart.sol#L131](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L131)

## Description

The ```resetBeat``` function in ```Heart.sol``` may be used to decrease the ```lastBeat``` variable so that the ```beat``` function may be called again. The ```lastBeat``` variable will be set to ```block.timestamp - frequency```. If the ```beat``` function is not called regularly enough such that ```beat``` may be called multiple times then the ```resetBeat``` function may set ```lastBeat``` to be more than it should.

## Impact

Callers of the ```beat``` function will lose out on rewards.

## Proof of Concept

Test code added to ```Heart.t.sol```:
```solidity
	function testResetBeatSetsIncorrectLastBeat() public {
        /// Get the beat frequency of the heart and wait that amount of time
        uint256 frequency = heart.frequency();
        vm.warp(block.timestamp + frequency*5);\

        vm.prank(guardian);
        heart.resetBeat();
        
        heart.beat();

        /// beat is only callable once instead of multiple times
        vm.expectRevert(abi.encodeWithSignature("Heart_OutOfCycle()"));
        heart.beat();
    }
```

## Recommended Mitigation Steps

Use the ```lastBeat``` value instead of ```block.timestamp``` when calculating the new ```lastBeat``` value in ```resetBeat```






# [L-02] ```cushionFactor``` not checked in constructor

## Line References

[Operator.sol#L134](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L134)

[Operator.sol#L516-L524](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L516-L524)

## Description

In ```Operator.sol```, the ```_config.cushionFactor``` has limits when set using the ```setCushionFactor``` function but these limits are not enforced in the constructor.

## Impact

A ```cushionFactor``` value that is too low or too high may lead to unexpected behaviour in the contract such as [market capacity for a new bond market being set to 0](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L388-L391).

## Proof of Concept

```solidity
    function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {
        /// Confirm factor is within allowed values
        if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();

        /// Set factor
        _config.cushionFactor = cushionFactor_;

        emit CushionFactorChanged(cushionFactor_);
    }
```

## Recommended Mitigation Steps

Use the checks used in ```setCushionFactor``` in the constructor.



# [L-03] ```beat``` function uncallable if no reward tokens in the contract

## Line References

[Heart.sol#L92-L114](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L92-L114)

## Description

The ```beat``` function in ```Heart.sol``` is used to update values used throughout the protocol such as the moving average and price ranges. The ```Heart.sol``` contract sets a ```reward``` token which is used to incentivize callers of the ```beat``` function. Since the reward tokens are sent using ```safeTransfer```, the ```beat``` function reverts if the contract does not contain enough reward tokens.

## Impact

If the ```beat``` function is not called for some time certain values used in the protocol such as the moving average may be incorrect which may lead to unexpected errors. Although the function could be called again if the contract received more tokens, in the meantime the rest of the protocol would operate with outdated values.


## Proof of Concept

Test code added to ```Heart.t.sol```:

```solidity
	function testCannotBeatIfNoRewards() public {
        /// Get the beat frequency of the heart and wait that amount of time
        uint256 frequency = heart.frequency();
        vm.warp(block.timestamp + frequency);

        /// Deplete contracts reward tokens
        vm.prank(guardian);
        heart.withdrawUnspentRewards(rewardToken);

        /// Beat the heart
        vm.expectRevert("TRANSFER_FAILED");
        heart.beat();
    }

```

The test code above shows that after the contract is drained of reward tokens (```withdrawUnspentRewards``` is called) the ```beat``` function reverts when called.

## Recommended Mitigation Steps

Consider allowing the ```beat``` function to be called when there is no reward tokens in the contract. This could be done by adding a parameter to the function to indicate if the caller would like to receive the reward. If not, the ```_issueReward``` function would not be called in ```beat``` and can be successfully called without issuing rewards.



# [N-01] Unable to revoke endorsements

## Line References

[Governance.sol#L180-L201](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L180-L201)

## Description

In ```Governance.sol```, users are able to endorse proposals with their balance of ```VOTES```. In order for a proposal to be activated, it must have endorsements more than or equal to the endorsement threshold. 

## Impact

If a user calls ```endorseProposal``` on the wrong proposal or realizes that they do not support the  validity of the proposal, they are unable to revoke their endorsement.

## Proof of Concept

```solidity
    function endorseProposal(uint256 proposalId_) external {
        uint256 userVotes = VOTES.balanceOf(msg.sender);

        if (proposalId_ == 0) {
            revert CannotEndorseNullProposal();
        }

        Instruction[] memory instructions = INSTR.getInstructions(proposalId_);
        if (instructions.length == 0) {
            revert CannotEndorseInvalidProposal();
        }

        // undo any previous endorsement the user made on these instructions
        uint256 previousEndorsement = userEndorsementsForProposal[proposalId_][msg.sender];
        totalEndorsementsForProposal[proposalId_] -= previousEndorsement;

        // reapply user endorsements with most up-to-date votes
        userEndorsementsForProposal[proposalId_][msg.sender] = userVotes;
        totalEndorsementsForProposal[proposalId_] += userVotes;

        emit ProposalEndorsed(proposalId_, msg.sender, userVotes);
    }
```

## Recommended Mitigation Steps

Consider adding functionality that allows a user to revoke their endorsements.