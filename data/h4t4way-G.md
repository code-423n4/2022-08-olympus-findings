# LOOP CONSUMING EXCESSIVE GAS

Hi Team. 

Prices per computational step on Ethereum are orders of magnitude higher than with centralized providers. 
Moreover, Ethereum miners impose a limit on the total number of Gas consumed in a block. 
If `array.length` is large enough, the function exceeds the block gas limit, and transactions calling it will never be confirmed.

This becomes a security issue, if an external actor influences array.length.


E.g., if an array enumerates all registered addresses, an adversary can register many addresses, causing the problem described above.

**File: src/Kernel.sol**

```
 for (uint256 i; i < depLength; ) {
            Keycode keycode = dependencies[i];

            moduleDependents[keycode].push(policy_);
            getDependentIndex[keycode][policy_] = moduleDependents[keycode].length - 1;

            unchecked {
                ++i;
            }
        }
```


## Remedation

Either explicitly or just due to normal operation, the number of iterations in a loop can grow beyond the block gas limit, which can cause the complete contract to be stalled at a certain point. Therefore, loops with a bigger or unknown number of steps should always be avoided.