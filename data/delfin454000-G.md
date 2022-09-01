### Array length should not be looked up in every iteration of a `for` loop
Since calculating the array length costs gas, it's best to read the length of the array from memory before executing the loop.
___
[Governance.sol: L278-283](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L278-L283)
```solidity
        for (uint256 step; step < instructions.length; ) {
            kernel.executeAction(instructions[step].action, instructions[step].target);
            unchecked {
                ++step;
            }
        }
```
Suggestion:
```solidity
        uint256 instructLength = instructions.length;
        for (uint256 step; step < instructLength; ) {
            kernel.executeAction(instructions[step].action, instructions[step].target);
            unchecked {
                ++step;
            }
        }
```
___
___


### Use `++i` instead of `i++` to increase count in a `for` loop
Since use of  `i++` (or equivalent counter) costs more gas, it is better to use `++i`
___
[KernelUtils.sol: L43-51](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L43-L51)
```solidity
    for (uint256 i = 0; i < 5; ) {
        bytes1 char = unwrapped[i];

        if (char < 0x41 || char > 0x5A) revert InvalidKeycode(keycode_); // A-Z only

        unchecked {
            i++;
        }
    }
```
Suggestion:
```solidity
    for (uint256 i = 0; i < 5; ) {
        bytes1 char = unwrapped[i];

        if (char < 0x41 || char > 0x5A) revert InvalidKeycode(keycode_); // A-Z only

        unchecked {
            ++i;
        }
    }
```
___
Similarly for the following `for` loop:

[KernelUtils.sol: L58-66](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L58-L66)
___
___