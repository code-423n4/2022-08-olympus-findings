# Executive Summary

The idea of Modules and Policies is brilliant!

Most of the codebase is well written and well thought out, the one exception to me was Governance which I don't believe will withstand an adversarial environment.

Minor Code smells are listed below rated via the following standard

## Legend:

- L -> Low Severity -> Could cause issues however impact / probability is limited
- R -> Refactoring -> Suggested Code Change to improve readability and maintainability or to offer better User Experience
- NC -> Non-Critical / Informational -> No risk of loss, pertains to events or has no impact

## L - Burning `VOTES` from Governance will break accounting

While burning `VOTES` from the `Governance` contract is questionable, the code has no check to prevent that.

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L38-L42

```solidity

    function burnFrom(address wallet_, uint256 amount_) external permissioned {
        _burn(wallet_, amount_);
    }

```

Because `Governance` and `VOTES.transferFrom` relies on a "use -> refund" pattern, losing even 1 wei of token will cause `reclaimVotes` to revert, effectively denying a user from being able to vote again.

Voting can be denied by simply burning their `VOTES` hence why I set the severity to Low as this is a Ban with extra steps as the `voter_admin` can just burn the votes from the user

## L - Allow others to repay the debt

`repayLoan` allows only the caller to repay their own debt, this can create situations in which insolvency or a smart contract bug prevent from making the TRSRY whole.

A straightforward solution would be to allow anyone to repay the loan on behalf of a specific address

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L103-L110

```solidity

    /// @notice Lets an address with debt repay their loan.
    function repayLoan(ERC20 token_, uint256 amount_) external nonReentrant {
        if (reserveDebt[token_][msg.sender] == 0) revert TRSRY_NoDebtOutstanding();

        // Deposit from caller first (to handle nonstandard token transfers)
        uint256 prevBalance = token_.balanceOf(address(this));
        token_.safeTransferFrom(msg.sender, address(this), amount_);
```


By allowing other addresses a softer approach to repaying debt can be achieved.

This avoids having to manually reset the debt.


## L - `_activatePolicy` is non CEI conformant

The function `_activatePolicy` will perform an external call to `policy_.configureDependencies()` and then it will change storage.

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L298-L315

```solidity
        // Add policy to list of active policies
        activePolicies.push(policy_);
        getPolicyIndex[policy_] = activePolicies.length - 1;

        // Record module dependencies
        Keycode[] memory dependencies = policy_.configureDependencies();
        uint256 depLength = dependencies.length;

        for (uint256 i; i < depLength; ) {
            Keycode keycode = dependencies[i];

            moduleDependents[keycode].push(policy_);
            getDependentIndex[keycode][policy_] = moduleDependents[keycode].length - 1;

            unchecked {
                ++i;
            }
        }
```

I wasn't able to find any exploit as the function is privileged

## R - `get` for a state changing function

`getXyz` is typically used for retrieving values from view functions, however in the case of `TRSRY` the function is used to receive a loan.

Because the codebase already uses `get` for view functions, I'd recommend renaming the function below to `receiveLoan` or just `loan` to keep the coding convention

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L92-L93

```solidity
    function getLoan(ERC20 token_, uint256 amount_) external permissioned {

```


## R - Can check contract existence without assembly

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L31-L37

```solidity
function ensureContract(address target_) view {
    uint256 size;
    assembly {
        size := extcodesize(target_)
    }
    if (size == 0) revert TargetNotAContract(target_);
}
```

Can be changed to
```solidity
target_.code.length
```


## Lack of Address(0) Zero-Checks

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L66-L67

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L77-L78


## NC - Lack of event for setters

Throughout the codebase, most setters emit events, however `setActiveStatus` doesn't

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L127-L128

```solidity
        isActive = activate_;

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L77-L78

## NC - Events not emitted in constructor

While setters emit events, the constructor doesn't, this may cause issues with tracking, e.g. theGraph as an event is for the initial setting is not emitted


https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L217-L220

```solidity
    constructor() {
        executor = msg.sender;
        admin = msg.sender;
    }
```

## NC - Gibberish action will still emit an event

You may instead want to emit only when a valid action is executed
Or add a comment to the function mentioning that

As it stands the code will emit even if the action data is not recognized

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L259-L260

```solidity
        emit ActionExecuted(action_, target_);

```