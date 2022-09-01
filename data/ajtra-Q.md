# Summary
## Low
[L01] A floating pragma is set.
[L02] A different pragma is set.

## Non Critical
[NC01] Use of block timestamp

# Low
## [L01] A floating pragma is set.
### Description
The current pragma Solidity directive is >=0.8.0" for some contracts. It is recommended to specify a fixed compiler version to ensure that the bytecode produced 
does not vary between builds. 
This is especially important if you rely on bytecode-level verification of the code.

### Mitigation
Lock the pragma.

### Lines in the code
[IBondCallback.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/interfaces/IBondCallback.sol#L2)
[IHeart.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IHeart.sol#L2)
[IOperator.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L2)

## [L02] A different pragma is set.
### Description
The current pragma Solidity directive is "0.8.15" but there is others contracts that is using a different pragma version ">=0.8.0".
It's cleaner to use the same versions.

### Lines in the code
[INSTR.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/INSTR.sol#L2)
[MINTR.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/MINTR.sol#L2)
[TRSRY.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/TRSRY.sol#L2)
[RANGE.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L2)
[PRICE.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L2)
[VOTES.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/VOTES.sol#L2)
[Kernel.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L2)
[KernelUtils.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/utils/KernelUtils.sol#L2)
[TreasuryCustodian.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/TreasuryCustodian.sol#L2)
[Operator.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L2)
[BondCallback.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/BondCallback.sol#L2)
[Heart.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Heart.sol#L2)
[Governance.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L2)
[PriceConfig.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/PriceConfig.sol#L2)
[VoterRegistration.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/VoterRegistration.sol#L2)
[IHeart.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IHeart.sol#L2)
[IOperator.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L2)
[IBondCallback.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/interfaces/IBondCallback.sol#L2)


# Non Critical
## [NC01] Use of block timestamp
### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), 
locking funds for periods of time, and various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Recommendation
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor 
(either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, 
or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; 
with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change 
a contract state can be more secure, as miners are unable to easily manipulate the block number.

### Lines in the code
[Heart.sol#63](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Heart.sol#L63)
[Heart.sol#94](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Heart.sol#L94)
[Heart.sol#131](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Heart.sol#L131)
[Governance.sol#L171](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L171)
[Governance.sol#L212](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L212)
[Governance.sol#L227](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L227)
[Governance.sol#L231](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L231)
[Governance.sol#L272](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L272)
[PRICE.sol#L143](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L143)
[PRICE.sol#L146](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L146)
[PRICE.sol#L165](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L165)
[PRICE.sol#L171](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L171)
[PRICE.sol#L215](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L215)
[RANGE.sol#L85](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L85)
[RANGE.sol#L92](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L92)
[RANGE.sol#L136](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L136)
[RANGE.sol#L138](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L138)
[RANGE.sol#L148](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L148)
[RANGE.sol#L150](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L150)
[RANGE.sol#L191](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L150)
[RANGE.sol#L200](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L200)
[RANGE.sol#L207](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L207)
[RANGE.sol#L231](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L231)
[RANGE.sol#L233](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L233)
[Operator.sol#L210](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L210)
[Operator.sol#L216](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L216)
[Operator.sol#L404](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L404)
[Operator.sol#L456](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L456)
[Operator.sol#L708](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L708)
[Operator.sol#L720](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L720)



