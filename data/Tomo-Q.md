# QA Report


## ✅ L-1: Unsafe use of transfer()/transferFrom() with IERC20

### 📝 Description
Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens.
example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert.

### 💡 Recommendation
Use OpenZeppelin's `SafeERC20` safeTransfer()/safeTransferFrom() instead

### 🔍 Findings:
```2022-08-olympus/blob/main/src/policies/Governance.sol#L259``` [VOTES.transferFrom(msg.sender, address(this), userVotes);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L259 )

```2022-08-olympus/blob/main/src/policies/Governance.sol#L312``` [VOTES.transferFrom(address(this), msg.sender, userVotes);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L312 )


## ✅ L-2: `_safeMint()` should be used rather than `_mint()` wherever possible

### 📝 Description
`_mint()` is discouraged in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. 

### 💡 Recommendation
You should change from` _mint` to `_safeMint` in terms of security.

### 🔍 Findings:
```2022-08-olympus/blob/main/src/modules/VOTES.sol#L36``` [`_mint(wallet_, amount_);`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L36 )


## ✅ N-1: Do not use Deprecated Library Functions

### 📝 Description
[Deprecated](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L44) in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead

### 💡 Recommendation
Use `safeIncreaseAllowance()` and `safeDecreaseAllowance()` instead of `safeApprove`

### 🔍 Findings:
```2022-08-olympus/blob/main/src/policies/BondCallback.sol#L57``` [ohm.safeApprove(address(MINTR), type(uint256).max);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L57 )

```2022-08-olympus/blob/main/src/policies/Operator.sol#L167``` [ohm.safeApprove(address(MINTR), type(uint256).max);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L167 )


## ✅ N-2: Non-assembly method available

### 📝 Description
You don't need to use assembly. `assembly` should be avoided as much as possible due to various risks

### 💡 Recommendation
You can change as follows.
 `assembly{ id := chainid() } => uint256 id = block.chainid`, `assembly { size := extcodesize() } => uint256 size = address().code.length`

### 🔍 Findings:
```2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L34``` [size := extcodesize(target_)](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L34 )

## ✅ N-3: Non-library/interface files should use fixed compiler versions, not floating ones

### 📝 Description
Non-library/interface files should use fixed compiler versions, not floating ones

### 💡 Recommendation
Delete the floating keyword `^`.

### 🔍 Findings:
```2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L2``` [pragma solidity >=0.8.0;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L2 )

```2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#L2``` [pragma solidity >=0.8.0;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#L2 )

```2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L2``` [pragma solidity >=0.8.0;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L2 )


## ✅ N-4: Variable names that consist of all capital letters should be reserved for `constant/immutable` variables

### 📝 Description
Variable names that consist of all capital letters should be reserved for `constant/immutable` variables

### 💡 Recommendation
Variables that are not constant/immutable should be declared in lower case

### 🔍 Findings:
```2022-08-olympus/blob/main/src/policies/BondCallback.sol#L29``` [OlympusTreasury public TRSRY;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L29 )

```2022-08-olympus/blob/main/src/policies/BondCallback.sol#L30``` [OlympusMinter public MINTR;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L30 )

```2022-08-olympus/blob/main/src/policies/Governance.sol#L56``` [OlympusInstructions public INSTR;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L56 )

```2022-08-olympus/blob/main/src/policies/Governance.sol#L57``` [OlympusVotes public VOTES;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L57 )

```2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L10``` [OlympusVotes public VOTES;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L10 )


## ✅ N-5: Open Todos

### 📝 Description
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

### 💡 Recommendation
Delete TODO keyword

### 🔍 Findings:
```2022-08-olympus/blob/main/src/policies/Operator.sol#L657``` [/// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L657 )

```2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L51``` [// TODO Currently allows anyone to revoke any approval EXCEPT activated policies.](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L51 )

```2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L52``` [// TODO must reorg policy storage to be able to check for deactivated policies.](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L52 )

```2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L56``` [// TODO Make sure `policy_` is an actual policy and not a random address.](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L56 )


## ✅ N-6: No use of two-phase ownership transfers

### 📝 Description
Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call `accept*()` to become the new owner. This prevents passing the ownership to an account that is unable to use it.

### 💡 Recommendation
Consider implementing a two step process where the admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of admin to fully succeed. This ensures the nominated EOA account is a valid and active account.

### 🔍 Findings:

```2022-08-olympus/blob/main/src/Kernel.sol#L253``` [admin = target_;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L253 )


## ✅ N-7: Use a more recent version of solidity

### 📝 Description
Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked (<bytes>, <bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked (<str>, <str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

### 💡 Recommendation
Use more recent version of solidity.

### 🔍 Findings:

```2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L2``` [pragma solidity >=0.8.0;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L2 )

```2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#L2``` [pragma solidity >=0.8.0;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#L2 )

```2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L2``` [pragma solidity >=0.8.0;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L2 )


