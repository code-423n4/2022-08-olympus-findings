
## Summary

G-01 Using calldata instead of memory for read-only arguments in external functions saves gas (1 instance)
G-02 <x> += <y> costs more gas than <x> = <x> + <y> for state variables (12 instances)
G-03 internal functions only called once can be inlined to save gas (2 instances)
G-04 Using bools for storage incurs overhead (11 instances)
G-05 Using > 0 costs more gas than != 0 when used on a uint in a require() statement (1 instance0
G-06 ++i costs less gas than i++, especially when it’s used in for-loops  (2 instances)
G-07 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead (88 instances)
G-08 Using private rather than public for constants, saves gas (9 instances)
G-09 Empty blocks should be removed or emit something (2 instances) 
G-10 calldata instead of memory for read-only function parameter (5 instances)

Total: 133 instances in 10 issues


## G-01 Using calldata instead of memory for read-only arguments in external functions saves gas
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

1 instance in 1 file:
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L162


## G-02 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

12 instances in 5 files:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L96
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L97
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L222

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L58

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L144

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L198
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L252
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L254


## G-03 internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

2 instances in 2 files:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L391-395

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L137-L141


## G-04 Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled. https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use uint256(1) and uint256(2) for true/false

11 instances in 5 files:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L113
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L181
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L194
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L197

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L62

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L63
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L66

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L24

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L33

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L105
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L117


## G-05 Using > 0 costs more gas than != 0 when used on a uint in a require() statement

This change saves 6 gas per instance

1 instance in 1 file:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L247


## G-06 ++i costs less gas than i++, especially when it’s used in for-loops (--i/i-- too)

Saves 6 gas PER LOOP

2 instances in 1 file:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64


## G-07 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

88 instances in 10 files:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L100

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L51

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L25

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L45
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L115
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L148
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L200

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L27
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L28
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L44
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L47
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L50
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L53
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L56
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L75
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L76
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L84
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L87
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L97
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L113
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L127
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L185
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L240
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L266
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L289

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L27

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L28

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L51
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L52
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L53
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L54
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L83
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L86
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L97
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L108
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L116
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L127
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L128
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L129
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L210
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L216
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L375
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L376
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L377
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L403
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L404
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L418
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L455
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L456
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L528
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L529
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L530
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L535
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L536
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L548
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L560
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L561
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L562
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L665
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L705
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L707
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L708
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L717
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L719
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L720

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L58
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L69

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L13
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L14
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L15
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L16
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L17
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L18
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L19
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L20
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L31
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L32
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L33
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L85
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L93
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L94
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L95
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L101
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L110
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L111
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L112


## G-08 Using private rather than public for constants, saves gas
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

9 instances in 4 files:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L65

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L121
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L124
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L127
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L130
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L133
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L137



## G-09 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

2 instances in 1 file:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L139
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L143


## G-10 calldata instead of memory for read-only function parameter

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

5 instances in 3 files:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L139
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L143
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L393

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L53
