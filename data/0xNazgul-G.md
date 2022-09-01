## [NAZ-G1] Moving `if (proposalHasBeenActivated[proposalId_] == true)` 
**Context**: [`Governance.sol#L230-L232`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L230-L232)

**Description**:
Moving:
```js
if (proposalHasBeenActivated[proposalId_] == true) {
	revert ProposalAlreadyActivated();
}
```
earlier in `activateProposal()` will make it fail sooner and save gas.

**Recommendation**: 
Consider moving `if (proposalHasBeenActivated[proposalId_] == true)` earlier in `activateProposal()`


## [NAZ-G2] State Variables That Can Be Set To `Immutable`
**Context**: [`BondCallback.sol#L28`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L28), [`BondCallback.sol#L32`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L32)

**Description**:
Solidity `0.6.5` introduced `immutable` as a major feature. It allows setting contract-level variables at construction time which gets stored in code rather than storage. Each call to it reads from storage, using a `sload` costing 2100 gas cold or 100 gas warm. Setting it to `immutable` will have each storage read of the state variable to be replaced by the instruction `push32 value`, where `value` is set during contract construction time and this costs only 3 gas.

**Recommendation**: 
Set the state variable to `immutable`.


## [NAZ-G3] Right Shift Instead of Dividing By 2
**Context**: [`Operator.sol#L372`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L372), [`Operator.sol#L427`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L427)

**Description**:
The `SHR` opcode is 3 gas cheaper than `DIV` and also bypasses Solidity's division by 0 prevention overhead.

**Recommendation**: 
Consider using right shift instead of dividing by 2.


## [NAZ-G4] Functions Visibility Can Be Declared External
**Context**: [`Kernel.sol#L439`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439), [`Kernel.sol#L451`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L451), [`TRSRY.sol#L75`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L75), [`MINTR.sol#L33`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L33), [`MINTR.sol#L37`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L37), [`RANGE.sol#L215`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L215), [`VOTES.sol#L45`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L45), [`VOTES.sol#L51`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L51), [`INSTR.sol#L37`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L37), [`Governance.sol#L145`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L145), [`Governance.sol#L151`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L151)

**Description**:
Several functions across multiple contracts have a public visibility and can be marked with external visibility to save gas. 

**Recommendation**: 
Change the functions visibility to external to save gas.


## [NAZ-G5] Use `calldata` Instead of `memory` For Function Parameters
**Context**: [`TreasuryCustodian.sol#L53`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L53), [`BondCallback.sol#L152`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L152)

**Description**:
The dynamic array arr has the storage location memory. When the function gets called externally, the array values are kept in calldata and copied to memory during ABI decoding (using the opcode calldataload and mstore). And during the for loop, arr[i] accesses the value in memory using a mload.

**Recommendation**: 
Use `calldata` instead of `memory` for function parameters to avoid using memory with array values when a function is getting called externally.


## [NAZ-G6] For array elements, `arr[i] = arr[i] + 1` is cheaper than `arr[i] += 1`
**Context**: [`TRSRY.sol#L96-L97`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L96-L97), [`TRSRY.sol#L131`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131), [`VOTES.sol#L58`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L58), [`BondCallback.sol#L143-L144`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143-L144), [`Governance.sol#L198`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L198), [`Governance.sol#L252`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L252), [`Governance.sol#254`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L254)

**Description**:
Due to stack operations this is 25 gas cheaper when dealing with arrays in storage, and 4 gas cheaper for memory arrays.

**Recommendation**: 
Use `arr[i] = arr[i] + 1` instead of `arr[i] += 1` when dealing with arrays


## [NAZ-G7] Use `++index` instead of `index++` to increment a loop counter
**Context**: [`KernelUtils.sol#L49`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49), [`KernelUtils.sol#L64`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64)

**Description**:
Due to reduced stack operations, using `++index` saves 5 gas per iteration.

**Recommendation**: 
Use `++index `to increment a loop counter.


## [NAZ-G8] Use of `2**256 - 1 && type(uint256).max` When `2**255` Can Be Used
**Context**: [`TRSRY.sol#L147`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L147), [`RANGE.sol#L88`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L88), [`RANGE.sol#L95`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L95), [`RANGE.sol#L221`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L221), [`RANGE.sol#L230`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L230), [`Operator.sol#L167`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L167), [`Operator.sol#L477`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L477), [`Operator.sol#L603`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L603), [`BondCallback.sol#L57`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L57), [`BondCallback.sol#L95`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L95)

**Description**:
Infinity can also be represented via ``2**255`, it's hex representation is `0x8000000000000000000000000000000000000000000000000000000000000000` while `2**256 - 1` is `0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff`. Then main difference is and where the gas savings come from is, zeros are cheaper than non-zero values in hex representation.

**Recommendation**: 
Use `2**255` instead of `2**256 - 1` to save gas on deployment.


## [NAZ-G9] Setting The Constructor To Payable
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-olympus/tree/main/src)

**Description**:
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of `msg.value == 0` and saves 21 gas on deployment with no security risks.

**Recommendation**: 
Set the constructor to payable.


## [NAZ-G10] Function Ordering via Method ID
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-olympus/tree/main/src)

**Description**:
Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. One could use [`This tool`](https://emn178.github.io/solidity-optimize-name/) to help find alternative function names with lower Method IDs while keeping the original name intact.

**Recommendation**: 
Find a lower method ID name for the most called functions for example ```mostCalled()``` vs. ```mostCalled_41q()``` is cheaper by 44 gas.
