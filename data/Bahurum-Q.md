## [L-01]: Upgrades to `VOTES` module can lead to governance vulnerabilities
The comment in [VOTES.sol#L10](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L10):
```
/// @dev    This is currently a substitute module that stubs gOHM.
```
means that the `VOTES` module is temporary and will be replaced by a form of governance token (could be staked gOHM for example). One must be sure before upgrading that the new token does not present a `transfer` function nor any mechanism or bypass that effectively allows to transfer tokens in between addresses. If the contrary is true, it would lead to a double endorsing issue (possibility to call [`OlympusGovernance.endorseProposal()`](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L181) a second time after getting the same tokens to a different address). Note that a bypass that allows transfer of tokens without delay could be used by an attacker to get unlimited endorsement power and execute a DoS on the governance by frontrunning calls to `activateProposal()` with a self-endorsed proposal.

## [L-02]: Voting temporarily reduces endorsing power
When calling `vote()` on the governance, `VOTES` token are transfered temporarily to the governance contract ([Governance.sol#L259](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L259)), so one must wait before being able to endorse a  proposal again.

## [L-03]: Missing events for critical changes in system parameters
Change of critical system parameters should come with an event emission to allow for monitoring of potentially dangerous or malicious changes. Occurrencies of this issue are [Kernel.sol#L77](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L77), [Kernel.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L127), [BondCallback.sol#L190](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L190)

## [L-04]: `executor` can become also `admin`
At [Kernel.sol#L253](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L253) the `executor` is allowed to become also `admin` of the `Kernel`. Consider checking that the `admin` set is not the `executor` (and vice versa at [Kernel.sol#L251](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L251)) to avoid full centralization of the system.

## [L-05]: Open `TODO`s
Open TODOs can point to architecture or programming issues that still need to be resolved. Occurrencies at [TreasuryCustodian.sol#L51-L56](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol#L51-L56), [Operator.sol#L657](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L657). All issues raised in TODOs should be addressed before deployment.

## [NC-01]: Missing/Incomplete/Incorrect Natspec comments:

- missing Natspec comments for public / external functions:
  - [TRSRY.sol#L59](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L59)
  - [MINTR.sol#L33](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/MINTR.sol#L33)
  - [MINTR.sol#L37](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/MINTR.sol#L37)
  - [VOTES.sol#L35](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L35)
  - [VOTES.sol#L39](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L39)
  - [Governance.sol#L61](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L61)
  - [Governance.sol#L70](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L70)

- missing Natspec comments for public getters: 
  - [Kernel.sol#L63](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L63)
  - [MINTR.sol#L9](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/MINTR.sol#L9)
  - [RANGE.sol#L65](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L65)
  - [BondCallback.sol#L24-L26](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L24-L26)
  - [Governance.sol#L56-L57](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L56-L57)
  - [Operator.sol#L83](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L83)
  - [Operator.sol#L86](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L86)
  - [Operator.sol#L89](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L89)
  
- missing function parameters:
  - [Kernel.sol#L75](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L75)
  - [Kernel.sol#L126](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L126)
  - [Kernel.sol#L234](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L234)
  - [INSTR.sol#36](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/INSTR.sol#L36)
  - [INSTR.sol#42](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/INSTR.sol#L42)
  - [TRSRY.sol#L63](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L63)
  - [TRSRY.sol#L74](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L74)
  - [TRSRY.sol#L92](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L92)
  - [TRSRY.sol#L105](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L105)
  - [TRSRY.sol#L121](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L121)
  - [VOTES.sol#L45](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L45)
  - [VOTES.sol#L50](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L50)
  - [Governance.sol#L144](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L144)

- missing function return values
  - [INSTR.sol#42](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/INSTR.sol#L42)
  - [RANGE.sol#L274](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L274)
  - [RANGE.sol#L279](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L279)
  - [RANGE.sol#L289](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L289)
  - [RANGE.sol#L300](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L300)
  - [RANGE.sol#L318](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L318)
  - [RANGE.sol#L329](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L329)
  - [RANGE.sol#L339](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L339)
  - [Governance.sol#L144](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L144)
  - [Governance.sol#L150](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L150)
  - [IHeart.sol#L17](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/interfaces/IHeart.sol#L17)
  - [IOperator.sol#L143](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/interfaces/IOperator.sol#L143)

- incorrect comments:
  - [Kernel.sol#L167](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L167): should be `Mapping of keycode to module address`
  - [Kernel.sol#L170](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L170): should be `Mapping of keycode to module address`
  - [Kernel.sol#L180](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L180): `Policy` and `Keycode` should be inverted


Consider adding missing Natspec comments according to the relevant [section](https://docs.soliditylang.org/en/v0.8.16/natspec-format.html#natspec) in the solidity docs.

## [NC-02]: Redundant code:
At [Governance.sol#L223](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L223) and [Governance.sol#L306](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L306) use of `bool == true` is equivalent to just `bool`.

## [NC-03]: Use custom error when `netVotes` should underflow
In [QA.md#L82](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/audit/QA.md#L82) check that `yesVotesForProposal[activeProposal.proposalId] > noVotesForProposal[activeProposal.proposalId]` before subtracting and revert with custom error `NotEnoughVotesToExecute` if not. This avoids to have a Panic error due to underflow in case `netVotes` should underflow.