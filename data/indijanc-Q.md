## No check for 0 address in `executeAction()`
[Kernel.sol L251](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L251)
[Kernel.sol L253](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L253)

No check for 0 address on change executor and change admin instruction. A mistake would lock the contract, unable to take actions. This seems to be used during deployment where such a mistake is super small, and during a governance proposal execution where such a mistake is a bit more likely. The latter however is protected by `ensureContract(instruction.target)` on storing the instructions in `store()` [INSTR.sol L52](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L52). Consider adding `ensureContract(target_)` in `executeAction()` for executor and admin if those are meant to be contracts.

## Moving average parameters in `OlympusPrice` contract are set independently
[PRICE.sol L240](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L240)
[PRICE.sol L266](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L266)

It feels like `movingAverageDuration()` and `observationFrequency()` should be modified together because of the divisible check.
Consider scenario:
Current state is duration = 9 and frequency = 3
Instruction to change that to duration = 8 and frequency = 2
This would require 3 instructions:
- duration = 12, frequency = 3
- duration = 12, frequency = 2
- duration = 8, frequency = 2

This seems like a waste of gas, and somewhat confusing instructions in such cases.

Consider combining this into a single function `changeMovingAverageParams()` to set both parameters.