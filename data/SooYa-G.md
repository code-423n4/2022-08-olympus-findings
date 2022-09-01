
## <X> += <Y> COSTS MORE GAS THAN <x> = <X> + <Y> FOR STATE VARIABLES
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L194
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L198
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L252
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L254
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L144
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L222
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L138


## FOR LOOPS CAN BE MORE EFFICIENT 
To optimize the for loop and make it consume less gas, i suggest to:

1. If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas. 

2. Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration. I suggest storing the array’s length in a variable before the for-loop.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L43
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L58

```for (uint256 i = 0; i < 32; ) {```

Actually this solution is already done in other contracts, so i suggest to change the line code above.