# [G-01] Redundant zero initialization

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas.

Locations where this was found include
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol#L43
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol#L58
https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/lib/UserFactory.sol#L25
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L397

## Recommended Mitigation Steps

Remove the redundant zero initialization
`uint256 i;` instead of `uint256 i = 0;`

# [G-02] Use prefix not postfix in loops

Using a prefix increment (++i) instead of a postfix increment (i++) saves gas for each loop cycle and so can have a big gas impact when the loop executes on a large number of elements.

Locations where this was found include
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol#L49
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol#L64

## Recommended Mitigation Steps

Use prefix not postfix to increment in a loop

# [G-03] Use iszero assembly for zero checks

Comparing a value to zero can be done using the `iszero` EVM opcode. This can save gas

Source from t11s https://twitter.com/transmissions11/status/1474465495243898885

Locations where this was found include
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L79
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L185
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L221
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L242
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L268
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L106
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/INSTR.sol#L48
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L565
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L183
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L188
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L243
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L298
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol#L36

## Recommended Mitigation Steps

Use the assembly `iszero` evm opcode to compare values to zero

# [G-04] Add payable to functions that won't receive ETH

Identifying a function as payable saves gas. Functions that have a modifier like onlyOwner or auth cannot be called by normal users and will not mistakenly receive ETH. These functions can be payable to save gas.

There are many functions that have the auth modifier in the contracts. Some examples are
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L439
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L451

## Recommended Mitigation Steps

Add payable to these functions for gas savings

# [G-05] Add payable to constructors that won't receive ETH

Identifying a constructor as payable saves gas. Constructors should only be called by the admin or deployer and should not mistakenly receive ETH. Constructors can be payable to save gas.

Locations where this was found include
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L15
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L71
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L77
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L16
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L45
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/INSTR.sol#L20
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L24
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L38
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/VoterRegistration.sol#L16
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L15
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L92
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L59
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L54
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L65
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L85
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L115
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L217

## Recommended Mitigation Steps

Add payable to these functions for gas savings

# [G-06] Use internal function in place of modifier

An internal function can save gas vs. a modifier. A modifier inlines the code of the original function but an internal function does not.

Source https://blog.polymath.network/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size-c44580b218e6#dde7

Locations where this was found include
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L70
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L88
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L119
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L223
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L229

## Recommended Mitigation Steps

Use internal functions in place of modifiers to save gas.

# [G-07] Use uint not bool

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

Locations where this was found include
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L62
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L63
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L66
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L33
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L113
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L207
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L394

## Recommended Mitigation Steps

Replace bool variables with uints

# [G-08] Use uint256 not smaller ints

From [the solidity docs](https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html?highlight=elements%20that%20are%20smaller%20than%2032%20bytes#layout-of-state-variables-in-storage)
```
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
```

Locations where this was found include
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L44
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L47
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L50
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L53
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L56
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L59

## Recommended Mitigation Steps

Replace smaller int or uint variables with uint256 variables

# [G-09] Use simple comparison in if statement

The comparison operators >= and <= use more gas than >, <, or ==. Replacing the  >= and ≤ operators with a comparison operator that has an opcode in the EVM saves gas

The existing code is 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L683-L693
```
if (currentPrice <= movingAverage) {
    if (!regen.observations[regen.nextObservation]) {
        _status.high.observations[regen.nextObservation] = true;
        _status.high.count++;
    }
} else {
    if (regen.observations[regen.nextObservation]) {
        _status.high.observations[regen.nextObservation] = false;
        _status.high.count--;
    }
}
```

A simple comparison can be used for gas savings by reversing the logic
```
if (currentPrice > movingAverage) {
    if (regen.observations[regen.nextObservation]) {
        _status.high.observations[regen.nextObservation] = false;
        _status.high.count--;
    }
} else {
    if (!regen.observations[regen.nextObservation]) {
        _status.high.observations[regen.nextObservation] = true;
        _status.high.count++;
    }
}
```

## Recommended Mitigation Steps

Replace the comparison operator and reverse the logic to save gas using the suggestions above

# [G-10] Bitshift for divide by 2

When multiply or dividing by a power of two, it is cheaper to bitshift than to use standard math operations.

There is a divide by 2 operation on these lines
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L372
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L427

## Recommended Mitigation Steps

Bitshift right by one bit instead of dividing by 2 to save gas

# [G-11] Non-public variables save gas

Many constant variables are public, but changing the visibility of these variables to private or internal can save gas.

Locations where this was found include
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L59
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L65
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L89
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L121
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L124
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L127
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L130
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L133
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L137
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L9
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L68
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L71
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L82
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L83
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L85
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L86

## Recommended Mitigation Steps

Declare some public variables as private or internal to save gas

# [G-12] Write contracts in vyper

The contracts are all written entirely in solidity. Writing contracts with vyper instead of solidity can save gas.

Source https://twitter.com/eiber_david/status/1515737811881807876
doggo demonstrates https://twitter.com/fubuloubu/status/1528179581974417414?t=-hcq_26JFDaHdAQZ-wYxCA&s=19

## Recommended Mitigation Steps

Write some or all of the contracts in vyper to save gas