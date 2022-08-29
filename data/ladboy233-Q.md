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

