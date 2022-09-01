## Non-critical

### Misleading comment, should be `// a-z and _ only`

https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L61

## Low

### Protocol executor can exercise any of the admin functions, since he has the ability to switch roles including the admin role itself. The comment prior should specify this case scenario.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439

### Someone can be given the roles "Admin" or "Executor" or variations with different letter casings, without any of the admin or executor functions. The exercising of the protocol is not affected though this ambiguity can be avoided.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439

### Instruction ID 0 is skipped

In order to not skip id 0, use `totalInstructions++` instead of `++totalInstructions`

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L44

### Unreachable portions of the code should be deleted

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L47

Notice that removing this line of code can also save deployment gas.