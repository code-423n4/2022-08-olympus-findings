https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol
1. unchecked : activePolicies.lenght-1 cannot underflow since element is pushed in previous statement

``` git diff
diff --git a/src/Kernel.sol b/src/Kernel.sol
index 3a00ec5..c9a3536 100644
--- a/src/Kernel.sol
+++ b/src/Kernel.sol
@@ -297,7 +297,10 @@ contract Kernel {
 
         // Add policy to list of active policies
         activePolicies.push(policy_);
-        getPolicyIndex[policy_] = activePolicies.length - 1;
+        //activePolicies.lenght-1 cannot underflow since element is pushed in previous statement
+        unchecked {
+            getPolicyIndex[policy_] = activePolicies.length - 1;
+        }
 
         // Record module dependencies
         Keycode[] memory dependencies = policy_.configureDependencies();
```

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol
1. unchecked : totalInstructions cannot practically reach uint max

``` git diff
diff --git a/src/modules/INSTR.sol b/src/modules/INSTR.sol
index 4536df2..56825ae 100644
--- a/src/modules/INSTR.sol
+++ b/src/modules/INSTR.sol
@@ -41,7 +41,10 @@ contract OlympusInstructions is Module {
     /// @notice Store a list of Instructions to be executed in the future.
     function store(Instruction[] calldata instructions_) external permissioned returns (uint256) {
         uint256 length = instructions_.length;
-        uint256 instructionsId = ++totalInstructions;
+        uint256 instructionsId;
+        unchecked {
+            instructionsId = ++totalInstructions;
+        }
 
         Instruction[] storage instructions = storedInstructions[instructionsId];
```

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol
1.  Use local variables to refer storage variables

``` git diff
diff --git a/src/modules/RANGE.sol b/src/modules/RANGE.sol
index e878bac..5ff2cef 100644
--- a/src/modules/RANGE.sol
+++ b/src/modules/RANGE.sol
@@ -161,19 +161,20 @@ contract OlympusRange is Module {
         uint256 cushionSpread = _range.cushion.spread;
 
         // Calculate new wall and cushion values from moving average and spread
-        _range.wall.low.price = (movingAverage_ * (FACTOR_SCALE - wallSpread)) / FACTOR_SCALE;
-        _range.wall.high.price = (movingAverage_ * (FACTOR_SCALE + wallSpread)) / FACTOR_SCALE;
+        //Use local variables to refer storage variables
+        uint256 _r_wall_low_price = _range.wall.low.price = (movingAverage_ * (FACTOR_SCALE - wallSpread)) / FACTOR_SCALE;
+        uint256 _r_wall_high_price = _range.wall.high.price = (movingAverage_ * (FACTOR_SCALE + wallSpread)) / FACTOR_SCALE;
 
-        _range.cushion.low.price = (movingAverage_ * (FACTOR_SCALE - cushionSpread)) / FACTOR_SCALE;
-        _range.cushion.high.price =
+        uint256 _r_cushion_low_price = _range.cushion.low.price = (movingAverage_ * (FACTOR_SCALE - cushionSpread)) / FACTOR_SCALE;
+        uint256 _r_cushion_high_price = _range.cushion.high.price =
             (movingAverage_ * (FACTOR_SCALE + cushionSpread)) /
             FACTOR_SCALE;
 
         emit PricesChanged(
-            _range.wall.low.price,
-            _range.cushion.low.price,
-            _range.cushion.high.price,
-            _range.wall.high.price
+            _r_wall_low_price ,
+            _r_cushion_low_price,
+            _r_cushion_high_price,
+            _r_wall_high_price
         );
     }
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol
1. Unchecked

``` git diff
diff --git a/src/modules/TRSRY.sol b/src/modules/TRSRY.sol
index de9b630..7ec43e1 100644
--- a/src/modules/TRSRY.sol
+++ b/src/modules/TRSRY.sol
@@ -109,11 +109,14 @@ contract OlympusTreasury is Module, ReentrancyGuard {
         uint256 prevBalance = token_.balanceOf(address(this));
         token_.safeTransferFrom(msg.sender, address(this), amount_);
 
-        uint256 received = token_.balanceOf(address(this)) - prevBalance;
+        uint256 received;
+        unchecked {
+            received = token_.balanceOf(address(this)) - prevBalance;
 
-        // Subtract debt from caller
-        reserveDebt[token_][msg.sender] -= received;
-        totalDebt[token_] -= received;
+            // Subtract debt from caller
+            reserveDebt[token_][msg.sender] -= received;
+            totalDebt[token_] -= received;
+        }
 
         emit DebtRepaid(token_, msg.sender, received);
     }

```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol
1. No need to initialize variable to 0.
2. use ++i instead of i++

``` git diff
diff --git a/src/utils/KernelUtils.sol b/src/utils/KernelUtils.sol
index 125a674..69f7ea4 100644
--- a/src/utils/KernelUtils.sol
+++ b/src/utils/KernelUtils.sol
@@ -40,13 +40,13 @@ function ensureContract(address target_) view {
 function ensureValidKeycode(Keycode keycode_) pure {
     bytes5 unwrapped = Keycode.unwrap(keycode_);
 
-    for (uint256 i = 0; i < 5; ) {
+    for (uint256 i; i < 5; ) {
         bytes1 char = unwrapped[i];
 
         if (char < 0x41 || char > 0x5A) revert InvalidKeycode(keycode_); // A-Z only
 
         unchecked {
-            i++;
+            ++i;
         }
     }
 }
@@ -55,13 +55,13 @@ function ensureValidKeycode(Keycode keycode_) pure {
 function ensureValidRole(Role role_) pure {
     bytes32 unwrapped = Role.unwrap(role_);
 
-    for (uint256 i = 0; i < 32; ) {
+    for (uint256 i; i < 32; ) {
         bytes1 char = unwrapped[i];
         if ((char < 0x61 || char > 0x7A) && char != 0x5f && char != 0x00) {
             revert InvalidRole(role_); // a-z only
         }
         unchecked {
-            i++;
+            ++i;
         }
     }
 }
```

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol
1. Use unchecked for decimals increment
2. Use pre increment/decrement
``` git diff
diff --git a/src/policies/Operator.sol b/src/policies/Operator.sol
index 7573526..d8576c1 100644
--- a/src/policies/Operator.sol
+++ b/src/policies/Operator.sol
@@ -485,7 +485,9 @@ contract Operator is IOperator, Policy, ReentrancyGuard {
         int8 decimals;
         while (price_ >= 10) {
             price_ = price_ / 10;
-            decimals++;
+            unchecked {
+                ++decimals;
+            }
         }
 
         /// Subtract the stated decimals from the calculated decimals to get the relative price decimals.
@@ -683,12 +685,12 @@ contract Operator is IOperator, Policy, ReentrancyGuard {
         if (currentPrice <= movingAverage) {
             if (!regen.observations[regen.nextObservation]) {
                 _status.high.observations[regen.nextObservation] = true;
-                _status.high.count++;
+                ++_status.high.count;
             }
         } else {
             if (regen.observations[regen.nextObservation]) {
                 _status.high.observations[regen.nextObservation] = false;
-                _status.high.count--;
+                --_status.high.count;
             }
         }
```

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol
1. Use local variable to refer storage variable
``` git diff
diff --git a/src/policies/BondCallback.sol b/src/policies/BondCallback.sol
index 4da1a3a..f9fdab1 100644
--- a/src/policies/BondCallback.sol
+++ b/src/policies/BondCallback.sol
@@ -153,10 +153,12 @@ contract BondCallback is Policy, ReentrancyGuard, IBondCallback {
         ERC20 token;
         uint256 balance;
         uint256 len = tokens_.length;
+        //Use local variable to refer storage variable
+        OlympusTreasury _TRSRY = TRSRY;
         for (uint256 i; i < len; ) {
             token = tokens_[i];
             balance = token.balanceOf(address(this));
-            token.safeTransfer(address(TRSRY), balance);
+            token.safeTransfer(address(_TRSRY), balance);
             priorBalances[token] = token.balanceOf(address(this));
 
             unchecked {
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol
1. Use local variable instead of read from memory
``` git diff
diff --git a/src/policies/Governance.sol b/src/policies/Governance.sol
index 8829e3b..31fc309 100644
--- a/src/policies/Governance.sol
+++ b/src/policies/Governance.sol
@@ -275,7 +275,9 @@ contract OlympusGovernance is Policy {
 
         Instruction[] memory instructions = INSTR.getInstructions(activeProposal.proposalId);
 
-        for (uint256 step; step < instructions.length; ) {
+        // Use local variable instead of read from memory
+        uint256 _len = instructions.length;
+        for (uint256 step; step < _len; ) {
             kernel.executeAction(instructions[step].action, instructions[step].target);
             unchecked {
                 ++step;
```