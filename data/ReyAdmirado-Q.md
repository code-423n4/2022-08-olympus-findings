| | issue |
| ----------- | ----------- |
| 1 | [typo in comments](#1-typo-in-comments) |
| 2 | [event is missing indexed fields](#2-event-is-missing-indexed-fields) |
| 3 | [_safemint() should be used rather than _mint() wherever possible](#3-safemint-should-be-used-rather-than-mint-wherever-possible) |
| 4 | [constants should be defined rather than using magic numbers ](#4-constants-should-be-defined-rather-than-using-magic-numbers) |
| 5 | [lines are too long](#5-lines-are-too-long) |
| 6 | [event is missing old value ](#6-event-is-missing-old-value) |
| 7 | [function should have emit](#7-function-should-have-emit) |
| 8 | [inconsistent use of named return variables](#8-inconsistent-use-of-named-return-variables) |
| 9 | [open todos](#9-open-todos) |


## 1. typo in comments

numbe --> number

- [PRICE.sol#L126](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L126)

deactive --> deactivate

- [Operator.sol#L295](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L295)
- [Operator.sol#L326](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L326)


## 2. event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

- [PRICE.sol#L26](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L26)

- [RANGE.sol#L20](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L20)
- [RANGE.sol#L21](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L21)
- [RANGE.sol#L22](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L22)
- [RANGE.sol#L24](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L24)

- [TRSRY.sol#L20](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L20)
- [TRSRY.sol#L27](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L27)
- [TRSRY.sol#L28](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L28)
- [TRSRY.sol#L29](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L29)

- [Kernel.sol#L203](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L203)

- [Governance.sol#L87](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L87)
- [Governance.sol#L89](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L89)

- [Operator.sol#L45](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L45)
- [Operator.sol#L52](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L52)
- [Operator.sol#L54](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L54)


## 3. _safemint() should be used rather than _mint() wherever possible

- [VOTES.sol#L36](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L36)


## 4. constants should be defined rather than using magic numbers 

Even assembly can benefit from using readable constants instead of hex/numeric literals

- [PRICE.sol#L90](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L90)
- [PRICE.sol#L165](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L165)

- [RANGE.sol#L245-248](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L245)
- [RANGE.sol#L264](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L264)

- [Governance.sol#L164](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L164)
- [Governance.sol#L217](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L217)
- [Governance.sol#L268](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L268)

- [BondCallback.sol#L49](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L49)
- [BondCallback.sol#L71](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L71)


## 5. lines are too long

Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

- [PRICE.sol#L31](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L31)

- [RANGE.sol#L40](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L40)
- [RANGE.sol#L45](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L45)
- [RANGE.sol#L61](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L61)

- [IOperator.sol#L15](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L15)
- [IOperator.sol#L90](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L90)

- [Operator.sol#L97](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L97)
- [Operator.sol#L675](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L675)


## 6. event is missing old value 

throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. here emit is missing the old value.

- [RANGE.sol#L256](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L256)
- [RANGE.sol#L267](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L267)

- [TRSRY.sol#L134](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L134)


## 7. function should have emit

- [Kernel.sol#L126](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L126)


## 8. inconsistent use of named return variables

there is an inconsistent use of named return variables in the contract some functions return named variables, others return explicit values. consider adopting a consistent approach.
this would improve both the explicitness and readability of the code, and it may also help reduce regressions during future code refactors.

most files have functions without named return but here they are not:

- [Governance.sol#L61](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L61)
- [Governance.sol#L70](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L70)

- [Heart.sol#L69](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L69)
- [Heart.sol#L77](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L77)

- [Operator.sol#L154](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L154)
- [Operator.sol#L171](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L171)
- [Operator.sol#L276](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L276)


## 9. open todos

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

- [Operator.sol#L657](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L657)

- [TreasuryCustodian.sol#L51](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L51)
- [TreasuryCustodian.sol#L52](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L52)
- [TreasuryCustodian.sol#L56](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L56)
