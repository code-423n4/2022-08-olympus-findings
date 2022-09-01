## [G-01] onlyKernel MODIFIER IS NOT NEEDED FOR VIEW FUNCTIONS
Calling the following `requestPermissions` view functions, which are called by non-view functions, such as [`_activatePolicy`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L295-L323) and [`_deactivatePolicy`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L325-L346), will use gas. Because these `requestPermissions` functions have the `onlyKernel`, more run-time gas is used when calling these. Since view functions do not modify states, it is safe for these view functions to not use `onlyKernel`. Moreover, other similar view functions, such as [`Operator.requestPermissions`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L171-L186) do not use `onlyKernel` already. Please consider removing `onlyKernel` from the following view functions.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L60-L76
```solidity
    function requestPermissions()
        external
        view
        override
        onlyKernel
        returns (Permissions[] memory requests)
    {
        Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();
        Keycode MINTR_KEYCODE = MINTR.KEYCODE();

        requests = new Permissions[](4);
        requests[0] = Permissions(TRSRY_KEYCODE, TRSRY.setApprovalFor.selector);
        requests[1] = Permissions(TRSRY_KEYCODE, TRSRY.withdrawReserves.selector);
        requests[2] = Permissions(MINTR_KEYCODE, MINTR.mintOhm.selector);
        requests[3] = Permissions(MINTR_KEYCODE, MINTR.burnOhm.selector);
    }
```

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L70-L80
```solidity
    function requestPermissions()
        external
        view
        override
        onlyKernel
        returns (Permissions[] memory requests)
    {
        requests = new Permissions[](2);
        requests[0] = Permissions(INSTR.KEYCODE(), INSTR.store.selector);
        requests[1] = Permissions(VOTES.KEYCODE(), VOTES.transferFrom.selector);
    }
```

## [G-02] REVERT CHECK CAN BE PLACED AT START OF FUNCTION BODY IF POSSIBLE
When a `revert` check is allowed to be placed at the start of the function body, the subsequent operations that cost more gas are prevented from running if it does revert.

`if (length == 0) revert INSTR_InstructionsCannotBeEmpty()` can be placed after `uint256 length = instructions_.length;` in the following code.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L42-L79
```solidity
    function store(Instruction[] calldata instructions_) external permissioned returns (uint256) {
        uint256 length = instructions_.length;
        uint256 instructionsId = ++totalInstructions;

        Instruction[] storage instructions = storedInstructions[instructionsId];

        if (length == 0) revert INSTR_InstructionsCannotBeEmpty();

        ...
    }
```

`if (activeProposal.proposalId == 0) { revert NoActiveProposalDetected(); }` and `if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) { revert UserAlreadyVoted(); }` can be placed above `uint256 userVotes = VOTES.balanceOf(msg.sender);` in the following code.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L240-L262
```solidity
    function vote(bool for_) external {
        uint256 userVotes = VOTES.balanceOf(msg.sender);

        if (activeProposal.proposalId == 0) {
            revert NoActiveProposalDetected();
        }

        if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
            revert UserAlreadyVoted();
        }

        ...
    }
```

## [G-03] VARIABLE DOES NOT NEED TO BE INITIALIZED TO ITS DEFAULT VALUE
Explicitly initializing a variable with its default value costs more gas than uninitializing it. For example, `uint256 i` can be used instead of `uint256 i = 0` in the following code.
```solidity
Kernel.sol
  397: for (uint256 i = 0; i < reqLength; ) {

utils\KernelUtils.sol
  43: for (uint256 i = 0; i < 5; ) {
  58: for (uint256 i = 0; i < 32; ) {
```

## [G-04] ARRAY LENGTH CAN BE CACHED OUTSIDE OF LOOP
Caching the array length outside of the loop and using the cached length in the loop costs less gas than reading the array length for each iteration. For example, `instructions.length` in the following code can be cached outside of the loop like `uint256 instructionsLength = instructions.length`, and `i < instructionsLength` can be used for each iteration.
```solidity
policies\Governance.sol
  278: for (uint256 step; step < instructions.length; ) {
```

## [G-05] ++VARIABLE CAN BE USED INSTEAD OF VARIABLE++
++variable costs less gas than variable++. For example, `i++` can be changed to `++i` in the following code.
```solidity
policies\Operator.sol
  488: decimals++;
  670: _status.low.count++;
  686: _status.high.count++;
  
utils\KernelUtils.sol
  49: i++;
  64: i++;
```

## [G-06] X = X + Y CAN BE USED INSTEAD OF X += Y
x = x + y costs less gas than x += y. For example, `balanceOf[to_] += amount_` can be changed to `balanceOf[to_] = balanceOf[to_] + amount_` in the following code.
```solidity
modules\PRICE.sol
  136: _movingAverage += (currentPrice - earliestPrice) / numObs;
  222: total += startObservations_[i];

modules\TRSRY.sol
  96: reserveDebt[token_][msg.sender] += amount_;
  97: totalDebt[token_] += amount_;
  131: if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;

modules\VOTES.sol
  58: balanceOf[to_] += amount_;

policies\BondCallback.sol
  143: _amountsPerMarket[id_][0] += inputAmount_;
  144: _amountsPerMarket[id_][1] += outputAmount_;

policies\Governance.sol
  198: totalEndorsementsForProposal[proposalId_] += userVotes;
  252: yesVotesForProposal[activeProposal.proposalId] += userVotes;
  254: noVotesForProposal[activeProposal.proposalId] += userVotes;

policies\Heart.sol
  103: lastBeat += frequency();
```