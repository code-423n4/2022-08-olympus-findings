### Gas Optimizations
|        | Finding                                                                                                      |  Instances  |
|:-------|:-------------------------------------------------------------------------------------------------------------|:-----------:|
| [G-01] | Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate |      1      |
| [G-02] | Unnecessary init to 0                                                                                        |      1      |
| [G-03] | `bool` is gas inefficient when used in storage                                                               |      2      |
| [G-04] | `array.length` should be cached in `for` loop                                                                |      1      |
| [G-05] | use `transfer()` instead of `transferFrom()`                                                                 |      1      |
### Gas overview per contract
| Contract                                                                                                                                  |  Instances  |  Gas Ops  |
|:------------------------------------------------------------------------------------------------------------------------------------------|:-----------:|:---------:|
| [Kernel.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol)                  |      2      |     2     |
| [Governance.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol) |      2      |     2     |
| [TRSRY.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol)            |      1      |     1     |
| [Operator.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol)     |      1      |     1     |
## Gas Optimizations
### [G-01] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operatio
*1 instance of this issue has been found:*
###### [G-01] [TRSRY.sol#L33-L39](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L33-L39)
```solidity

    mapping(address => mapping(ERC20 => uint256)) public withdrawApproval;

    /// @notice Total debt for token across all withdrawals.
    mapping(ERC20 => uint256) public totalDebt;

    /// @notice Debt for particular token and debtor address
    mapping(ERC20 => mapping(address => uint256)) public reserveDebt;
```
### [G-02] Unnecessary init to 0
These values are set by default.
*1 instance of this issue has been found:*
###### [G-02] [Kernel.sol#L397-L398](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L397-L398)
```solidity

        for (uint256 i = 0; i < reqLength; ) {

```
### [G-03] `bool` is gas inefficient when used in storage
Instead use `uint256` values to represent true/false instead.
[Reference](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)
*2 instances of this issue have been found:*
###### [G-03] [Operator.sol#L62-L66](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L62-L66)
```solidity

    /// @notice    Whether the Operator has been initialized
    bool public initialized;

    /// @notice    Whether the Operator is active
    bool public active;
```
###### [G-03b] [Kernel.sol#L113-L114](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L113-L114)
```solidity

    bool public isActive;

```
### [G-04] `array.length` should be cached in `for` loop
Caching the length array would save gas on each iteration by not having to read from memory or storage multiple times.
Example:
```solidity
uint length = array.length;
for (uint i; i < length;){
		uncheck { ++i }
}
```
*1 instance of this issue has been found:*
###### [G-04] [Governance.sol#L278](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L278)
```solidity

instructions
```
### [G-05] use `transfer()` instead of `transferFrom()`
There is no need to use `transferFrom()` to transfer money INTO an account.
*1 instance of this issue has been found:*
###### [G-05] [Governance.sol#L312-L313](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L312-L313)
```solidity

        VOTES.transferFrom(address(this), msg.sender, userVotes);

```
