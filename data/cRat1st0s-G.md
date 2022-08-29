# 2022-08-olympus-code4rena QA Report

- [2022-08-olympus-code4rena QA Report](#2022-08-olympus-code4rena-qa-report)
  - [Files Description Table](#files-description-table)
  - [Gas Optimizations](#gas-optimizations)
    - [[G-01]: For-Loops: No need to explicitly initialize variables with default values](#g-01-for-loops-no-need-to-explicitly-initialize-variables-with-default-values)
      - [Impact](#impact)
      - [Code Affected](#code-affected)
      - [Mitigation](#mitigation)
      - [Tools used](#tools-used)
    - [[G-02]: `>=` is cheaper than `>`](#g-02--is-cheaper-than-)
      - [Impact](#impact-1)
      - [Code Affected](#code-affected-1)
      - [Mitigation](#mitigation-1)
        - [Example](#example)
      - [Tools used](#tools-used-1)

##  Files Description Table

| File Name                                          | SHA-1 Hash                               |
| -------------------------------------------------- | ---------------------------------------- |
| 2022-08-olympus/src/modules/PRICE.sol              | eb3c920eaaf30e31cffbef13d8510dc18341d5ab |
| 2022-08-olympus/src/Kernel.sol                     | 702fd864c142f5c93781482371d168379d6b10df |
| 2022-08-olympus/src/utils/KernelUtils.sol          | b103389226af6aa16880e2568c5de4de143d7950 |
| 2022-08-olympus/src/modules/INSTR.sol              | b2c9521b73b50db74fa17b59b12e9b25269a83cc |
| 2022-08-olympus/src/modules/RANGE.sol              | 3b34f485fcb242d7a254307b239f055524ed2e6b |
| 2022-08-olympus/src/modules/TRSRY.sol              | 7626a2b1c998b640c51d08c8e665498ba73efca0 |
| 2022-08-olympus/src/modules/VOTES.sol              | 5e22b6aff627c48b8cedabbede375c1f5a468985 |
| 2022-08-olympus/src/modules/MINTR.sol              | e3ba147c72850c7463b5a3da587a77550ad6da1e |
| 2022-08-olympus/src/modules/PRICE.sol              | eb3c920eaaf30e31cffbef13d8510dc18341d5ab |
| 2022-08-olympus/src/policies/PriceConfig.sol       | 988825fff850ed5efb9713ac352628ca77f78cbc |
| 2022-08-olympus/src/policies/BondCallback.sol      | 6be071dd7f9ccc578d929670bff27ed8f72a9f62 |
| 2022-08-olympus/src/policies/TreasuryCustodian.sol | 752907434e36330542e6f3f18ae2e3a89e746c52 |
| 2022-08-olympus/src/policies/Governance.sol        | 88ae920ee84d217efdd686cb29939d820cbbd632 |
| 2022-08-olympus/src/policies/Operator.sol          | f185cfaa901424dd55c533b88a7b801f08b35367 |
| 2022-08-olympus/src/policies/VoterRegistration.sol | 74328138074d3796580439636955db37e4ffa9b2 |
| 2022-08-olympus/src/policies/Heart.sol             | f1a6dcb7778663cba55f2278e6e2d9044b7ec69c |


##  Gas Optimizations

### [G-01]: For-Loops: No need to explicitly initialize variables with default values

#### Impact
If a variable is not set/initialized, it is assumed to have the default value (`0`, `false`, `0x0`, etc depending on the data type). If you explicitly initialize it with its default value, you are just wasting gas.

#### Code Affected

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L397

```solidity
for (uint256 i = 0; i < reqLength; ) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L43

```solidity
for (uint256 i = 0; i < 5; ) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L58

```solidity
for (uint256 i = 0; i < 32; ) {
```

#### Mitigation
Do not initialize variables with default values.

#### Tools used
VS Code

### [G-02]: `>=` is cheaper than `>`

#### Impact
Non-strict inequalities (`>=`) are cheaper than strict ones (`>`). This is due to some supplementary checks (`ISZERO`, `3 gas`).

#### Code Affected

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L90

```solidity
if (exponent > 38) revert Price_InvalidParams();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L244-L249

```solidity
if (
    wallSpread_ > 10000 ||
    wallSpread_ < 100 ||
    cushionSpread_ > 10000 ||
    cushionSpread_ < 100 ||
    cushionSpread_ > wallSpread_
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L215

```solidity
if (startObservations_.length != numObs || lastObservationTime_ > uint48(block.timestamp))
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L264

```solidity
if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L212

```solidity
if (block.timestamp > proposal.submissionTimestamp + ACTIVATION_DEADLINE) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L103

```solidity
if (configParams[1] > uint256(7 days) || configParams[1] < uint256(1 days))
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L108

```solidity
if (configParams[3] < uint32(1 hours) || configParams[3] > configParams[1])
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L111

```solidity
if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L113-L115

```solidity
        if (
            configParams[5] < 1 hours ||
            configParams[6] > configParams[7] ||
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L237

```solidity
if (currentPrice > range.cushion.low.price || currentPrice < range.wall.low.price) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L243

```solidity
if (currentPrice < range.cushion.low.price && currentPrice > range.wall.low.price) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L254

```solidity
currentPrice < range.cushion.high.price || currentPrice > range.wall.high.price
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L262

```solidity
currentPrice > range.cushion.high.price && currentPrice < range.wall.high.price
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L518

```solidity
if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L533

```solidity
if (duration_ > uint256(7 days) || duration_ < uint256(1 days))
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L536

```solidity
if (depositInterval_ < uint32(1 hours) || depositInterval_ > duration_)
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L550

```solidity
if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L565

```solidity
if (wait_ < 1 hours || threshold_ > observe_ || observe_ == 0)
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L758

```solidity
if (amountOut > RANGE.capacity(false)) revert Operator_InsufficientCapacity();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L769

```solidity
if (amountOut > RANGE.capacity(true)) revert Operator_InsufficientCapacity();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L46

```solidity
if (char < 0x41 || char > 0x5A) revert InvalidKeycode(keycode_); // A-Z only
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L60

```solidity
if ((char < 0x61 || char > 0x7A) && char != 0x5f && char != 0x00) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L165

```solidity
if (updatedAt < block.timestamp - 3 * uint256(observationFrequency))
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L171

```solidity
if (updatedAt < block.timestamp - uint256(observationFrequency))
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L133

```solidity
if (capacity_ < _range.high.threshold && _range.high.active) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L145

```solidity
if (capacity_ < _range.low.threshold && _range.low.active) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L131

```solidity
if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L144

```solidity
if (approval < amount_) revert TRSRY_NotApproved();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L114

```solidity
if (quoteToken.balanceOf(address(this)) < priorBalances[quoteToken] + inputAmount_)
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L164

```solidity
if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L227

```solidity
if (block.timestamp < activeProposal.activationTimestamp + GRACE_PERIOD) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L268

```solidity
if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L272

```solidity
if (block.timestamp < activeProposal.activationTimestamp + EXECUTION_TIMELOCK) {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L94

```solidity
if (block.timestamp < lastBeat + frequency()) revert Heart_OutOfCycle();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L106

```solidity
if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L289

```solidity
if (amountOut < minAmountOut_)
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L320

```solidity
if (amountOut < minAmountOut_)
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L535

```solidity
if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L741

```solidity
RANGE.capacity(high_) < auctioneer.currentCapacity(market))
```

#### Mitigation
Replace `>` / `<` with `>=` / `<=` without breaking the logic of the code.

##### Example
Change
```solidity
if (exponent > 38) revert Price_InvalidParams();
```
to
```solidity
if (exponent >= 39) revert Price_InvalidParams();
```

#### Tools used
VS Code
