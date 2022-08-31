# KernerlUtils
## ensureValidKeycode: i++ 
Change ```i++``` for ```++i```. [POC](https://github.com/code-423n4/2022-01-yield-findings/issues/44)

## ensureValidRole: i++ 
Change ```i++``` for ```++i```. [POC](https://github.com/code-423n4/2022-01-yield-findings/issues/44)

# Kernel
## _activatePolicy: Math operation can be avoided Double access to activePolices can be avoided
It would be better to change [this](https://github.com/code-423n4/2022-08-olympus/blob/70d7259581fe32647293ca4ff653ca3f2ad770b6/src/Kernel.sol#L299-L300) for this:
```solidity
getPolicyIndex[policy_] = activePolicies.length;
activePolicies.push(policy_);
```

## _activatePolicy: Math operation and double access to activePolices can be avoided
It would be better to change [this](https://github.com/code-423n4/2022-08-olympus/blob/70d7259581fe32647293ca4ff653ca3f2ad770b6/src/Kernel.sol#L309-L310) for this:
```solidity
getDependentIndex[keycode][policy_] = moduleDependents[keycode].length;
moduleDependents[keycode].push(policy_);
```

## grantRole and revokeRole should be external
They can only be called by the admin, an external address. This will save gas.
[POC](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices)

# OlympusInstructions
## getInstruction should be external
This will save gas. It is not called by other function inside the contract
[POC](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices)

# OlympusRange
# updateCapacity: _range is modified and accesed multiples times
Copy it to a memory variable to lessen storage access.
```solidity
function updateCapacity(bool high_, uint256 capacity_) external permissioned {
    Range __range=_range;
    if (high_) {
        // Update capacity
        __range.high.capacity = capacity_;

        // If the new capacity is below the threshold, deactivate the wall if they are currently active
        if (capacity_ < __range.high.threshold && __range.high.active) {
            // Set wall to inactive
            __range.high.active = false;
            __range.high.lastActive = uint48(block.timestamp);

            emit WallDown(true, block.timestamp, capacity_);
        }
    } else {
        // Update capacity
        __range.low.capacity = capacity_;

        // If the new capacity is below the threshold, deactivate the wall if they are currently active
        if (capacity_ < __range.low.threshold && __range.low.active) {
            // Set wall to inactive
            __range.low.active = false;
            __range.low.lastActive = uint48(block.timestamp);

            emit WallDown(false, block.timestamp, capacity_);
        }
    }
    _range=__range;
}
```

## updatePrices: _range is modified and accessed multiples times
Copy it to a memory variable to lessen storage access.
```solidity
function updatePrices(uint256 movingAverage_) external permissioned {
    Range memory __range = range;
    // Cache the spreads
    uint256 wallSpread = __range.wall.spread;
    uint256 cushionSpread = __range.cushion.spread;

    // Calculate new wall and cushion values from moving average and spread
    __range.wall.low.price = (movingAverage_ * (FACTOR_SCALE - wallSpread)) / FACTOR_SCALE;
    __range.wall.high.price = (movingAverage_ * (FACTOR_SCALE + wallSpread)) / FACTOR_SCALE;

    __range.cushion.low.price = (movingAverage_ * (FACTOR_SCALE - cushionSpread)) / FACTOR_SCALE;
    __range.cushion.high.price =
        (movingAverage_ * (FACTOR_SCALE + cushionSpread)) /
        FACTOR_SCALE;

    emit PricesChanged(
        __range.wall.low.price,
        __range.cushion.low.price,
        __range.cushion.high.price,
        __range.wall.high.price
    );
    _range = __range;
}
```

## updateMarket should be external
This will save gas. It is not called by other function inside the contract
[POC](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices)

# OlympusTreasury
## repayLoan: can save gas if we check amount_ != 0
If amount_ == 0 is nonsense to continue the function execution. Adding ```require(amount_ != 0, 'amount_ can't be 0')``` can save gas for a wrong execution.

## reapyLoan: unnecessary calculations
Given that safeTransferFrom revert in case of an error, it seems nonsense the calculation of received. Meaning we can simplify our function to:
```solidity
function repayLoan(ERC20 token_, uint256 amount_) external nonReentrant {
    if (reserveDebt[token_][msg.sender] == 0) revert TRSRY_NoDebtOutstanding();

    // Deposit from caller first (to handle nonstandard token transfers)
    token_.safeTransferFrom(msg.sender, address(this), amount_);

    // Subtract debt from caller
    reserveDebt[token_][msg.sender] -= amount_;
    totalDebt[token_] -= amount_;

    emit DebtRepaid(token_, msg.sender, amount_);
}
```

# OlimpusGovernance
## endorseProposal: reduce totalEndorsementsForProposal\[proposalId_\] access and operation
We can grup the substraction and addition to avoid double memory access.

```solidity
// undo any previous endorsement the user made on these instructions
uint256 previousEndorsement = userEndorsementsForProposal[proposalId_][msg.sender];

// reapply user endorsements with most up-to-date votes
userEndorsementsForProposal[proposalId_][msg.sender] = userVotes;
totalEndorsementsForProposal[proposalId_] += (userVotes - previousEndorsement);
```

## vote: activeProposal is accessed multiple times
We can cache the state variable to avoid multiple storage access

## vote: userVotesForProposal\[activeProposal.proposalId\]\[msg.sender\] is accessed multiple times
We can cache the state variable to avoid multiple storage access

## executeProposal: activeProposal is accessed multiple times
We can cache the state variable to avoid multiple storage access


```solidity
function executeProposal() external {
    ActivatedProposal _activeProposal=activeProposal
    uint256 netVotes = yesVotesForProposal[_activeProposal.proposalId] -
        noVotesForProposal[_activeProposal.proposalId];
    if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {
        revert NotEnoughVotesToExecute();
    }

    if (block.timestamp < _activeProposal.activationTimestamp + EXECUTION_TIMELOCK) {
        revert ExecutionTimelockStillActive();
    }

    Instruction[] memory instructions = INSTR.getInstructions(_activeProposal.proposalId);

    for (uint256 step; step < instructions.length; ) {
        kernel.executeAction(instructions[step].action, instructions[step].target);
        unchecked {
            ++step;
        }
    }

    emit ProposalExecuted(_activeProposal.proposalId);

    // deactivate the active proposal
    activeProposal = ActivatedProposal(0, 0);
}
```