## EVENTS NOT EMITTED FOR IMPORTANT STATE CHANGES

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L498

```
 function setSpreads(uint256 cushionSpread_, uint256 wallSpread_)
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L510

```
 function setThresholdFactor(uint256 thresholdFactor_) external onlyRole("operator_policy") {
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L586

```
    function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_)
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L624

```
    function toggleActive() external onlyRole("operator_admin") {
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L130

```
    function resetBeat() external onlyRole("heart_admin") {
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L135

```
    function toggleBeat() external onlyRole("heart_admin") {
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L76

```
    function toggleBeat() external onlyRole("heart_admin") {
```

## approving MAX_UINT amount of ERC20 token is not safe

approving the maximum value of uint26 is a known practive to safe gas.

However , this pattern was proven to increase the impact of an attack many times in the pass.

in case the approved contract get hacked.

We recommand approving the exact amount that's needed to be transfered

or add an external function that allows the revovaction of approvals.

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L57

```
ohm.safeApprove(address(MINTR), type(uint256).max);
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L95

```
TRSRY.setApprovalFor(address(this), payoutToken, type(uint256).max);
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L147

```
      if (approval != type(uint256).max) {
            unchecked {
                withdrawApproval[withdrawer_][token_] = approval - amount_;
            }
        }
```

## Unhandled return value when doing transfer

we recommand that we handle the external function call return value in case of the slient failure 
of an external function.

We recommand use safeTransferFrom or safeTransfer instead of transfer or transferFrom

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L259

```
    VOTES.transferFrom(msg.sender, address(this), userVotes);
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L312

```
     VOTES.transferFrom(address(this), msg.sender, userVotes);
```
