QA Report
===


### Use a struct for config params in `Operator.sol`

**Severity**: Low

Using a struct would greatly increase the readability and reduce the chance of a mistake during deployment.

```diff
diff --git a/src/policies/Operator.sol b/src/policies/Operator.sol
index 7573526..1b70536 100644
--- a/src/policies/Operator.sol
+++ b/src/policies/Operator.sol
@@ -88,14 +88,26 @@ contract Operator is IOperator, Policy, ReentrancyGuard {
     /// Constants
     uint32 public constant FACTOR_SCALE = 1e4;

+    struct ConfigParams {
+        uint32 cushionFactor;
+        uint32 cushionDuration;
+        uint32 cushionDebtBuffer;
+        uint32 cushionDepositInterval;
+        uint32 reserveFactor;
+        uint32 regenWait;
+        uint32 regenThreshold;
+        uint32 regenObserve;
+    }
+
     /* ========== CONSTRUCTOR ========== */
     constructor(
         Kernel kernel_,
         IBondAuctioneer auctioneer_,
         IBondCallback callback_,
         ERC20[2] memory tokens_, // [ohm, reserve]
-        uint32[8] memory configParams // [cushionFactor, cushionDuration, cushionDebtBuffer, cushionDepositInterval, reserveFactor, regenWait, regenThreshold, regenObserve]
+        ConfigParams memory configParams
     ) Policy(kernel_) {
```
-----------------------

### Use constants for magic numbers

**Severity**: Low

Through the use of constants, certain limits and other values important to the protocol can be better managed and reduce chance of setting something incorrectly.

Here is an example of constants being used along with using a struct for ConfigParams

```diff
-        if (configParams[1] > uint256(7 days) || configParams[1] < uint256(1 days))
+        if (configParams.cushionDuration > MAX_CUSHION_DURATION || configParams.cushionDuration < MIN_CUSHION_DURATION )
             revert Operator_InvalidParams();

-        if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();
+        if (configParams.cushionDebtBuffer < MIN_DEBT_BUFFER) revert Operator_InvalidParams();

-        if (configParams[3] < uint32(1 hours) || configParams[3] > configParams[1])
+        if (configParams.cushionDepositInterval < MIN_CUSHION_DEPOSIT_INTERVAL || configParams.cushionDepositInterval > MAX_CUSHION_DEPOSIT_INTERVAL)
             revert Operator_InvalidParams();

```

-----------------------

### Using true/false to mean "High" / "Low" is an antipattern
**Severity**: Low

It is confusing to read something like:
`_regenerate(false)`

For better readability and to prevent possible mistakes in the future, use constants instead.

```solidity
bool constant HIGH = true;
bool constant LOW = false;

_regenerate(LOW)

```

-----------------------

### Use a local constant instead of calling an external contract.
**Severity**: Gas optimization

Instead of calling `PRICE.decimals()`, use a local constant for decimals.  Both the decimals in PRICE.sol and the decimals when calling PRICE.sol as an external contract can be imported from a `constants.sol` file thereby keeping the gas savings from the constant and also retaining only a single source of truth for the constant values.

