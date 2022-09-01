- [Low](#low)
    - [**1. Outdated compiler**](#1-outdated-compiler)
    - [**2. Wrong comment**](#2-wrong-comment)
    - [**3. Lack of checks**](#3-lack-of-checks)
    - [**4. Open TODO**](#4-open-todo)
    - [**5. Lack of event emmit**](#5-lack-of-event-emmit)
    - [**6. Unsafe math**](#6-unsafe-math)
- [Non critical](#non-critical)
    - [**7. Codding style**](#7-codding-style)
        - [Use a different file name than the contract name](#use-a-different-file-name-than-the-contract-name)
    - [**8. Integer overflow as error is not user friendly**](#8-integer-overflow-as-error-is-not-user-friendly)

# Low

## **1. Outdated compiler**

The pragma version used are:

```
pragma solidity >=0.8.0;
pragma solidity 0.8.15;
```

The minimum required version must be [0.8.16](https://github.com/ethereum/solidity/releases/tag/v0.8.16); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.3](https://blog.soliditylang.org/2021/03/23/solidity-0.8.3-release-announcement/):

- Optimizer: Fix bug on incorrect caching of Keccak-256 hashes.

[0.8.4](https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/):

- ABI Decoder V2: For two-dimensional arrays and specially crafted data in memory, the result of abi.decode can depend on data elsewhere in memory. Calldata decoding is not affected.

[0.8.9](https://blog.soliditylang.org/2021/09/29/solidity-0.8.9-release-announcement/):

- Immutables: Properly perform sign extension on signed immutables.
- User Defined Value Type: Fix storage layout of user defined value types for underlying types shorter than 32 bytes.

[0.8.13](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/):
- Code Generator: Correctly encode literals used in `abi.encodeCall` in place of fixed bytes arguments.

[0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/):

- ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against `calldatasize()` in all cases.
- Override Checker: Allow changing data location for parameters only when overriding external functions.

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

Apart from these, there are several minor bug fixes and improvements.

## **2. Wrong comment**

The comment of the `ensureValidRole` method of the `KernelUtils` contract is either incorrect or the logic does not match the comment, since the character '\0' and '_' are allowed, both of which are not mentioned in the comment.

**Recommended change:**

```diff
        if ((char < 0x61 || char > 0x7A) && char != 0x5f && char != 0x00) {
-           revert InvalidRole(role_); // a-z only
+           revert InvalidRole(role_); // a-z, _ or \0 only
        }
```

**Affected source code:**

- [KernelUtils.sol:61](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L61)

## **3. Lack of checks**

The following methods have a lack checks if the received argument is an address, it's good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than `address(0)`.

**Affected source code for `address(0)`:**

- [MINTR.sol:16](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/MINTR.sol#L16)
- [TRSRY.sol:65](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L65)
- [TRSRY.sol:66](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L66)
- [TRSRY.sol:76](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L76)
- [TRSRY.sol:77](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L77)
- [TRSRY.sol:92](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L92)
- [TRSRY.sol:105](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L105)
- [TRSRY.sol:123](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L123)
- [TRSRY.sol:124](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L124)
- [TRSRY.sol:138](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L138)
- [TRSRY.sol:139](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L139)
- [Heart.sol:56](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L56)
- [Heart.sol:57](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L57)
- [Heart.sol:140](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L140)
- [Heart.sol:150](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L150)
- [Kernel.sol:66](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L66)
- [Kernel.sol:77](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L77)

**Affected source code for array checks:**

The method `submitProposal` in the `Governance` contract doesn't check that the `instructions` array is empty. This check will be done in `endorseProposal`, so it's not needed to allow an empty proposal.

**Recommended change:**

```javascript
        if (instructions_.length == 0) {
            revert CannotEndorseInvalidProposal();
        }
```

- [Governance.sol:160](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L160)

## **4. Open TODO**

The code that contains "open todos" reflects that the development is not finished and that the code can change a posteriori, prior release, with or without audit.

**Affected source code:**

> // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.

- [TreasuryCustodian.sol:51](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol#L51)

> // TODO must reorg policy storage to be able to check for deactivated policies.

- [TreasuryCustodian.sol:52](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol#L52)

> // TODO Make sure `policy_` is an actual policy and not a random address.

- [TreasuryCustodian.sol:56](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol#L56)

> // TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?

- [Operator.sol:657](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L657)

## **5. Lack of event emmit**

The `Kernel.setActiveStatus` method does not emit an event when setting the `isActive` state variable, something that it's very important for dApps and users.

**Affected source code:**

- [Kernel.sol:127](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L127)

## **6. Unsafe math**

The `RANGE` contract has `regenerate` and `updatePrices` methods where a user-supplied argument can be passed as zero to the multiply operation.

This will affect range prices, so funds and trades could be affected if the `allowed` account decides to call this argument with 0 values.

Reference:
- https://slowmist.medium.com/analysis-of-the-treasuredao-zero-fee-exploit-73791f4b9c14

**Affected source code:**

- [RANGE.sol:164](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L164)
- [RANGE.sol:165](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L165)
- [RANGE.sol:167](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L167)
- [RANGE.sol:169](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L169)
- [RANGE.sol:185](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L185)

---

# Non critical

## **7. Codding style**

### Use a different file name than the contract name

This is a terrible practice that reduces the readability and auditability of the code.

**Affected source code:**

- `OlympusPriceConfig` is [PriceConfig.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/PriceConfig.sol#L7)
- `OlympusInstructions` is [INSTR.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/INSTR.sol#L10)
- `OlympusTreasury` is [TRSRY.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L17)
- `OlympusMinter` is [MINTR.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/MINTR.sol#L8) 
- `OlympusPrice` is [PRICE.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L22)
- `OlympusRange` is [RANGE.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L16)
- `OlympusVotes` is [VOTES.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L11)
- `OlympusHeart` is [Heart.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L21)

## **8. Integer overflow as error is not user friendly**

In the method `executeProposal` of `Governance` contract, an oveflow is raised when there are more NO votes than YES, that's not a good practice an it make worse the user experience, it's better to control this kind of errors in order to show an custom error.

```javascript
    function executeProposal() external {
        uint256 netVotes = yesVotesForProposal[activeProposal.proposalId] -
            noVotesForProposal[activeProposal.proposalId];
```

**Affected source code:**

- [Governance.sol:266-267](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L266-L267)
