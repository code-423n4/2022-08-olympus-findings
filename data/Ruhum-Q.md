# Report

- [Low](#low)
  - [L-01: several open TODOs](#l-01-several-open-todos)
  - [L-02: `Heart.lastBeat` doesn't represent the last beat](#l-02-heartlastbeat-doesnt-represent-the-last-beat)
  - [L-03: Governance voting system makes it difficult to endorse proposal while there's an active one](#l-03-governance-voting-system-makes-it-difficult-to-endorse-proposal-while-theres-an-active-one)
  - [L-04: High gas costs for voting on proposals will lower the rate of participation](#l-04-high-gas-costs-for-voting-on-proposals-will-lower-the-rate-of-participation)
  - [L-05: commented out logic in Deploy script sets the INSTR contract to be the executor](#l-05-commented-out-logic-in-deploy-script-sets-the-instr-contract-to-be-the-executor)
- [Non-Critical](#non-critical)
  - [N-01: emit events when changing a contract's configuration](#n-01-emit-events-when-changing-a-contracts-configuration)

# Low

## L-01: several open TODOs

```
./src/test/Kernel.t.sol:115:        // TODO test role not existing
./src/test/policies/Operator.t.sol:1556:    /// [X] setThresholdFactor (in Range.t.sol) TODO
./src/test/lib/bonds/bases/BondBaseTeller.sol:56:    ///      TODO update comment to reflect new format
./src/test/lib/bonds/bases/BondBaseTeller.sol:113:        /// TODO look at use cases to see if I need to provide referrer in to get total fees
./src/test/lib/quabi/Quabi.sol:61:    /// TODO get events, errors, state variables, etc.
./src/test/modules/PRICE.t.sol:368:    /// TODO: convert to vm.expectRevert
./src/test/modules/INSTR.t.sol:196:        // TODO update with correct error message
./src/test/modules/TRSRY.t.sol:93:    // TODO test if can withdraw more than allowed amount
./src/test/modules/TRSRY.t.sol:163:    // TODO test RepayLoan with no loan outstanding. should revert
./src/test/modules/MINTR.t.sol:100:    // TODO use vm.expectRevert() instead. Did not work for me.
./src/policies/Operator.sol:657:        /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
./src/policies/TreasuryCustodian.sol:51:    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
./src/policies/TreasuryCustodian.sol:52:    // TODO must reorg policy storage to be able to check for deactivated policies.
./src/policies/TreasuryCustodian.sol:56:        // TODO Make sure `policy_` is an actual policy and not a random address.
./src/scripts/Deploy.sol:145:            ] // TODO verify initial parameters
```

## L-02: `Heart.lastBeat` doesn't represent the last beat

The value is set to `lastBeat += frequency()`, see [here](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103), and `frequency()` represents the rate at which the function should be called. The moment you miss calling `beat()` the value of `lastBeat` will be outdated.

```
currentTimestamp = 1000 (easy number for this example)
lastBeat = 1000 
frequency = 13 (e.g. every block)

// you miss to call beat() once and then call it the next block
lastBeat = 1013
currentTimestamp = 1026
```
Meaning, lastBeat says that the `beat()` function was called at `1013` although it was called at `1026`. In that case, you're also able to call the function twice since the following check won't trigger a revert: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L94
Although, I don't see how that would cause an issue.

Instead, set the `lastBeat` to `block.timestamp` whenever `beat()` is called.

I know that there are economic incentives built-in to cause bots to call this function as soon as possible. That way you might prevent the `lastBeat` value from being out of sync. But, this solution isn't bulletproof. The economic costs (gas) of calling this function might be higher than the reward at a time when you would expect the function to be called. Since gas costs can't be predicted it would be difficult for you to anticipate and plan accordingly. So there might be a scenario where `beat()` isn't called as you'd expect it. This is a larger issue which I will also submit separately.

## L-03: Governance voting system makes it difficult to endorse proposal while there's an active one

Since you have to transfer your VOTES tokens to the Governance contract to vote, you're not able to endorse any other proposals while the tokens are locked up.

- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L259
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L181 (you can only endorse with your current balance which will be 0 after you've voted)

## L-04: High gas costs for voting on proposals will lower the rate of participation

To vote on a proposal you have to transfer your VOTES tokens to the governance contract. After the active proposal is finished, you have to reclaim your tokens to be able to use them again. This creates overhead which will lower the participation rate because of the high gas costs.

Instead, there shouldn't be any transfer or reclaiming at all. The user's voting power should be freed on the fly, e.g.:

```
if no active proposal:
    user has full voting power
else:
    if user voted already:
        no voting power
    else:
        full voting power
```

You just have to keep track of whether there is an active proposal and whether the user has voted already or not. It will save you a ton of gas and increase the overall participation rate. This will also fix the issue I mentioned in `L-03`. Since the user doesn't transfer their tokens to the governance contract, they can freely endorse proposals while there's an active one.

## L-05: commented out logic in Deploy script sets the INSTR contract to be the executor

That would brick the whole architecture since the INSTR contract is not able to execute the Kernel's `executeAction()` function. Since the logic was commented out, instead of deleted, I believe it's meant to be used at some point. So I decided to mention it here.

Instead, the executor role should be transferred to the Governance contract.

- https://github.com/code-423n4/2022-08-olympus/blob/main/src/scripts/Deploy.sol#L220
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L235

# Non-Critical

## N-01: emit events when changing a contract's configuration

It allows one to watch for these changes more easily.

- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L136
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L131
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L192
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L626
