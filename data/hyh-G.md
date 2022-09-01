The general case is always run, while there will be many one element operations, when it's excessive:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L417-L426

```solidity
            uint256 origIndex = getDependentIndex[keycode][policy_];
            Policy lastPolicy = dependents[dependents.length - 1];

            // Swap with last and pop
            dependents[origIndex] = lastPolicy;
            dependents.pop();

            // Record new index and delete deactivated policy index
            getDependentIndex[keycode][lastPolicy] = origIndex;
            delete getDependentIndex[keycode][policy_];
```

Consider optimizing for the one element case:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L417-L426

```solidity
            uint256 origIndex = getDependentIndex[keycode][policy_];
+           uint256 dependentsLength = dependents.length;
+           if (dependentsLength > 1) {
-               Policy lastPolicy = dependents[dependents.length - 1];
+               Policy lastPolicy = dependents[dependentsLength - 1];

                // Swap with last and pop
                dependents[origIndex] = lastPolicy;
                dependents.pop();

                // Record new index and delete deactivated policy index
                getDependentIndex[keycode][lastPolicy] = origIndex;
+           } else {
+               delete dependents;
+           }
            delete getDependentIndex[keycode][policy_];
```