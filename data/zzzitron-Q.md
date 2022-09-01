# Olympus DAO QA Report

# Summary

Risk | Title
---|---
L00 | Operator: incorrect accounting for fee-on-transfer reserve token
L01 | BondCallback: incorrect accounting if quoteToken is rebase token
L02 | PRICE: unsafe cast for `numObservations`
L03 | Operator: unsafe cast for decimals
L04 | BondCallback: operator is not set `constructor`
L05 | Operator: missing check for configParmas[0] (cushionFactor) in the constructor
L06 | Kernel: misplaced zero address check for `changeKernel`
L07 | Kernel: missing zero address check for `executor` and `admin`
L08 | INSTR, Governance: upon module's upgrade, all instruction data should be carried over to the new modules
L09 | BondCallback, Operator: upon module's upgrade, the token approval should be revoked
L10 | RANGE, PRICE: unused import of `FullMath`
L11 | Heart: if the `_issueReward` fails the heart beat will revert
L12 | PRICE: stale price

# Low

## L00 Operator: incorrect accounting for fee-on-transfer reserve token

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L330

If the reserve token is fee-to-transfer token and the user is buying ohm, the `Operator::swap` will incorrectly assume the `amountIn_` value is transferred, which fails to consider the fees. If the fee is rounded up, the attacker can purchase ohm without giving any assets to the treasury. It may not be profitable for the attacker, but it may cause devaluing of the ohm. However, the loss will be limited to the capacity.

```solidity
// Operator::swap
// if(tokenIn_ == reserve) : buying ohm
329             /// Transfer reserves to treasury
330             reserve.safeTransferFrom(msg.sender, address(TRSRY), amountIn_);
```


## L01 BondCallback: incorrect accounting if quoteToken is rebase token

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L114

If the quoteToken is rebase token, the priorBalances may change due to rebasing or airdrop. It may result to an incorrect accounting. However, whether it is exploitable depends on the Bond market's logic.
With the current logic, it just checks whether the balance is increased more than the `inputAmount_`, so it is harder to exploit, compare to the alternative logic of using the difference in balances as the input amount. However, it also introduces the possibility of paying the users less than they deserve.

```solidity
// Callback::callback
113         // Check that quoteTokens were transferred prior to the call
114         if (quoteToken.balanceOf(address(this)) < priorBalances[quoteToken] + inputAmount_)
115             revert Callback_TokensNotReceived();
```


## L02 PRICE: unsafe cast for `numObservations`

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L97
- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L249-L257
- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L281-L289


The `movingAverageDuration` and `observationFrequency` are uint48. So `movingAverageDuration / observationFrequency` may overflow when casted to uint32. In the below snippet, line 281, the array will be set based on the uint256 value, but the `numObservations` is casted down to uint32. It may result in different `numObservations` and the length of `observations` array.
However, given the large numbers, the attempt to set such a large number as the parameters will likely to fail with "out of gas" error, since the length of the array `observations` is ridiculously large in this case. Yet, it is probably safe to set some upper limit for the `numObservations` or use safeCast.

```solidity
// modules/PRICE::constructor
  97         numObservations = uint32(movingAverageDuration_ / observationFrequency_);

// modules/PRICE::changeObservationFrequency
  280         // Store blank observations array of new size
  281         observations = new uint256[](newObservations);
  282
  283         // Set initialized to false and update state variables
  284         initialized = false;
  285         lastObservationTime = 0;
  286         _movingAverage = 0;
  287         nextObsIndex = 0;
  288         observationFrequency = observationFrequency_;
  289         numObservations = uint32(newObservations);
```


## L03 Operator: unsafe cast for decimals

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L372
- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L427
- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L431-L434
- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L375-L379


In the `Operator::_activate` decimal values were casted to `int8` and `uint8` back and forth. Since there is no check, those values can potentially overflow/underflow. However, if it happens the exponent in the line 376 will like to revert due to too large numbers. Besides, if the price decimals are that big, this may not be the biggest problem to face.

```solidity
// policies/Operator.sol::_activate

372             int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);

375             uint256 oracleScale = 10**uint8(int8(PRICE.decimals()) - priceDecimals);
376             uint256 bondScale = 10 **
377                 uint8(
378                     36 + scaleAdjustment + int8(reserveDecimals) - int8(ohmDecimals) - priceDecimals
379                 );
```


## L04 BondCallback: operator is not set `constructor`

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L38-L45

If the `operator` is not set, the `callback` function will revert so, it is crucial to set the `operator` before any operation. However, it was not set in the `constructor`, but should be set separately by calling `setOperator`.


## L05 Operator: missing check for configParmas[0] (cushionFactor) in the constructor

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L92-L150
- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L516-L524

The `Operator::constructor` does not check the condition of the `cushionFactor`. Below is the condition for the `cushionFactor` checked in the `Operator::setCushionFactor`.

```solidity
// Operator::setCushionFactor

516     function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {
517         /// Confirm factor is within allowed values
518         if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();
519
520         /// Set factor
521         _config.cushionFactor = cushionFactor_;
522
523         emit CushionFactorChanged(cushionFactor_);
524     }
```


## L06 Kernel: misplaced zero address check for `changeKernel`

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L76-L78
- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L254-L257


Currently, the check for the `Kernel` to be a contract (also not to be the zero address), is in the current `Kernel` implementation. However, no modules and policies have the logic to ensure this as they inherit from `KernelAdapter`, which will just set the new kernel without a question. This will work well as long as the new Kernel has the similar logic to check the next Kernel's integrity. However, if the logic is forgotten, there is no other safe guard to ensure that the next kernel is not a zero address and is a contract.
Since `Kernel` is absolutely needed for this system's functionality, there is no possible case that the Kernel should be the zero address. Therefore, it is probably safe to add the checking logic to the `KernelAdapter`, so every module and policy will check for the next Kernel. It costs more gas since the check is done multiple times, but still arguably it is worth the cost, since Kernel is core part of the system and it will not updated very often.

```solidity
// KernelAdapter::changeKernel
 76     function changeKernel(Kernel newKernel_) external onlyKernel {
 77         kernel = newKernel_;
 78     }

// Kernel::executeAction
254         } else if (action_ == Actions.MigrateKernel) {
255             ensureContract(target_);
256             _migrateKernel(Kernel(target_));
257         }
```


## L07 Kernel: missing zero address check for `executor` and `admin`

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L250-L253

The `executor` and `admin` are not checked for the zero address when set by the `Kernel::executeAction`.

```solidity
// Kernel::executeAction
250         } else if (action_ == Actions.ChangeExecutor) {
251             executor = target_;
252         } else if (action_ == Actions.ChangeAdmin) {
253             admin = target_;
```


## L08 INSTR, Governance: upon module's upgrade, all instruction data should be carried over to the new modules

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L167
- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L187

The `Governance`'s logic will break if the `INSTR` module is upgraded to a new contract without having the same instructions data,  since the `proposalId`'s the `Governance` is using are bound to the `INSTR` module.


## L09 BondCallback, Operator: upon module's upgrade, the token approval should be revoked

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L57
- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L167 

The `BondCallback` and `Operator` approves ohm to the `MINTR` module in the `configureDependencies`. However, there is no logic to revoke those approvals (e.i. approve to zero). In the case of the `MINTR` has some bugs, it may be desirable to be able to revoke the approvals.

```solidity
// Operator::configureDependencies
167         ohm.safeApprove(address(MINTR), type(uint256).max);
```

## L10 RANGE, PRICE: unused import of `FullMath`

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L18
- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L23

The modules `RANGE` and `PRICE` imports `FullMath`, but it is not used.

```solidity
// modules/PRICE.sol
 22 contract OlympusPrice is Module {
 23     using FullMath for uint256;
```

## L11 Heart: if the issueReward fails the heart beat will revert

- https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L106

If the `_issueReward` reverts, for example, because the token balance is too low, the `beat` will as well revert, due to the `safeTransfer`. One might consider not to revert even in the case the `_issueReward` reverts.

## L12 PRICE: stale price

There is no indicator whether the price information is up-to-date. If the price information is not properly updated, the other contracts will keep using the data resulting in incorrect prices for swap.

<!-- zzzitron QA -->
