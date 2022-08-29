| | issue |
| ----------- | ----------- |
| 1 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (same with -= )](#1-x--y-costs-more-gas-than-x--x--y-for-state-variables-same-with) |
| 2 | [can make the variable outside the loop to save gas](#2-can-make-the-variable-outside-the-loop-to-save-gas) |
| 3 | [`++i` costs less gas than `i++`, especially when it’s used in for-loops (--i/i-- too)](#3-i-costs-less-gas-than-i-especially-when-its-used-in-for-loops---ii---too) |
| 4 | [it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied](#4-it-costs-more-gas-to-initialize-non-constantnon-immutable-variables-to-zero-than-to-let-the-default-of-zero-be-applied) |
| 5 | [using `calldata` instead of `memory` for read-only arguments in external functions saves gas](#5-using-calldata-instead-of-memory-for-read-only-arguments-in-external-functions-saves-gas) |
| 6 | [using `bools` for storage incurs overhead](#6-using-bools-for-storage-incurs-overhead) |
| 7 | [internal functions only called once can be inlined to save gas](#7-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 8 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#8-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 9 | [using private rather than public for constants, saves gas](#9-using-private-rather-than-public-for-constants-saves-gas) |
| 10 | [not using the named return variables when a function returns, wastes deployment gas](#10-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 11 | [state variables only set in the constructor should be declared](#11-state-variables-only-set-in-the-constructor-should-be-declared) |
| 12 | [`<array>.length` should not be looked up in every loop of a for-loop](#12-arraylength-should-not-be-looked-up-in-every-loop-of-a-for-loop) |



## 1. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (same with -= )

- [PRICE.sol#L136](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136)
- [PRICE.sol#L138](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L138)
- [PRICE.sol#L222](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L222)

- [TRSRY.sol#L96](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L96)
- [TRSRY.sol#L97](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L97)
- [TRSRY.sol#L115](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L115)
- [TRSRY.sol#L116](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L116)
- [TRSRY.sol#L131](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131)
- [TRSRY.sol#L132](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L132)

- [VOTES.sol#L56](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L56)
- [VOTES.sol#L58](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L58)

- [Governance.sol#L198](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L198)
- [Governance.sol#L252](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L252)
- [Governance.sol#L254](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L254)

- [BondCallback.sol#L143](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143)
- [BondCallback.sol#L144](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L144)

- [Heart.sol#L103](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103)


## 2. can make the variable outside the loop to save gas

- [KernelUtils.sol#L44](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L44)
- [KernelUtils.sol#L59](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L59)


## 3. `++i` costs less gas than `i++`, especially when it’s used in for-loops (--i/i-- too)

Saves 6 gas per loop

- [KernelUtils.sol#L49](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49)
- [KernelUtils.sol#L64](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64)

- [Operator.sol#L488](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L488)
- [Operator.sol#L670](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L670)
- [Operator.sol#L675](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L675)
- [Operator.sol#L686](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L686)
- [Operator.sol#L691](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L691)


## 4. it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied

- [Kernel.sol#L397](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L397)

- [KernelUtils.sol#L43](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L44)
- [KernelUtils.sol#L58](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L59)


## 5. using `calldata` instead of `memory` for read-only arguments in external functions saves gas

- [PRICE.sol#L205](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205)

- [Governance.sol#L159](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L159)

- [PriceConfig.sol#L45](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45)

- [TreasuryCustodian.sol#L53](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L53)

- [BondCallback.sol#L152](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L152)


## 6. using `bools` for storage incurs overhead

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

- [PRICE.sol#L62](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L62)

- [RANGE.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L127)
- [RANGE.sol#L184](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L184)
- [RANGE.sol#L216](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L216)
- [RANGE.sol#L281](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L281)
- [RANGE.sol#L291](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L291)
- [RANGE.sol#L302](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L302)
- [RANGE.sol#L320](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L320)
- [RANGE.sol#L330](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L330)
- [RANGE.sol#L340](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L340)

- [Kernel.sol#L113](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L113)
- [Kernel.sol#L126](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L126)
- [Kernel.sol#L181](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L181)
- [Kernel.sol#L194](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L194)
- [Kernel.sol#L197](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L197)
- [Kernel.sol#L394](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L394)

- [Governance.sol#L105](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L105)
- [Governance.sol#L117](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L117)
- [Governance.sol#L240](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L240)

- [BondCallback.sol#L24](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L24)

- [Heart.sol#L33](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L33)

- [Operator.sol#L63](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L63)
- [Operator.sol#L66](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L66)
- [Operator.sol#L363](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L363)
- [Operator.sol#L473](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L473)
- [Operator.sol#L618](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L618)
- [Operator.sol#L634](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L634)
- [Operator.sol#L699](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L699)
- [Operator.sol#L732](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L732)
- [Operator.sol#L735](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L735)
- [Operator.sol#L778](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L778)


## 7. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

- [Kernel.sol#L266](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L266)
- [Kernel.sol#L279](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L279)
- [Kernel.sol#L295](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L295)
- [Kernel.sol#L325](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L325)
- [Kernel.sol#L351](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L351)
- [Kernel.sol#L378](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L378)
- [Kernel.sol#L409](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L409)

- [Heart.sol#L111](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L111)

- [Operator.sol#L652](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L652)


## 8. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [PRICE.sol#L44](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L44)
- [PRICE.sol#L47](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L47)
- [PRICE.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L127)
- [PRICE.sol#L185](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L185)
- [PRICE.sol#L50](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L50)
- [PRICE.sol#L53](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L53)
- [PRICE.sol#L56](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L56)
- [PRICE.sol#L59](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59)
- [PRICE.sol#L75](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L75)
- [PRICE.sol#L76](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L76)
- [PRICE.sol#L205](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205)
- [PRICE.sol#L240](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L240)
- [PRICE.sol#L266](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L266)
- [PRICE.sol#L185](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L185)
- [PRICE.sol#L59](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59)
- [PRICE.sol#L84](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L84)
- [PRICE.sol#L87](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L87)

- [PriceConfig.sol#L45](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45)
- [PriceConfig.sol#L58](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L58)
- [PriceConfig.sol#L69](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L69)

- [Operator.sol#L89](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89)
- [Operator.sol#L97](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L97)
- [Operator.sol#L516](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L516)
- [Operator.sol#L528](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L528)
- [Operator.sol#L529](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L529)
- [Operator.sol#L530](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L530)
- [Operator.sol#L548](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L548)
- [Operator.sol#L560](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L560)
- [Operator.sol#L561](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L561)
- [Operator.sol#L562](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L562)
- [Operator.sol#L665](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L665)
- [Operator.sol#L83](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L83)
- [Operator.sol#L86](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L86)
- [Operator.sol#L418](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L418)


## 9. using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

- [PRICE.sol#L59](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59)

- [RANGE.sol#L65](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L65)

- [Governance.sol#L121](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L121)
- [Governance.sol#L124](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L124)
- [Governance.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L127)
- [Governance.sol#L130](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L130)
- [Governance.sol#L133](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L133)
- [Governance.sol#L137](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L137)

- [Operator.sol#L89](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89)


## 10. not using the named return variables when a function returns, wastes deployment gas

- [INSTR.sol#L28](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L28)

- [MINTR.sol#L25](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L25)

- [PRICE.sol#L113](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L113)

- [Kernel.sol#L100](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L100)

- [TRSRY.sol#L51](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L51)

- [RANGE.sol#L115](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L115)

- [VOTES.sol#L27](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L27)

- [BondCallback.sol#L177](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L177)


## 11. state variables only set in the constructor should be declared 

avoids a gsset (20000 gas)

- [Heart.sol#L48](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L48)

- [BondCallback.sol#L28](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L28)
- [BondCallback.sol#L32](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L32)


## 12. `<array>.length` should not be looked up in every loop of a for-loop

This reduce gas cost as show here https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5

- [Governance.sol#L278](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L278)

