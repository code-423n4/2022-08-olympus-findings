# **Low Risk Issues**

## **L001 - Unsafe ERC20 Operation(s)**

### **Description**

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's `SafeERC20` library or at least to wrap each operation in a `require` statement.

To circumvent ERC20's `approve` functions race-condition vulnerability use OpenZeppelin's `SafeERC20` library's `safe{Increase|Decrease}Allowance` functions.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.

[https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L259](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L259)

```solidity
VOTES.transferFrom(msg.sender, address(this), userVotes);
```

[https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L312](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L312)

```solidity
VOTES.transferFrom(address(this), msg.sender, userVotes);
```

# ****L002 - Unspecific Compiler Version Pragma****

### **Description**

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

[https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IHeart.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IHeart.sol#L2)

```solidity
pragma solidity >=0.8.0;
```

[https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IOperator.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IOperator.sol#L2)

```solidity
pragma solidity >=0.8.0;
```

[https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/interfaces/IBondCallback.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/interfaces/IBondCallback.sol#L2)

```solidity
pragma solidity >=0.8.0;
```

### Background info

[https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)

### Recommended Mitigation Steps

Lock the pragma version to the same version as used in the other contracts and also consider known bugs ([https://github.com/ethereum/solidity/releases](https://github.com/ethereum/solidity/releases)) for the compiler version that is chosen.

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile it locally.

## **L003 - Do not use Deprecated Library Functions**

### **Description**

The usage of deprecated library functions should be discouraged.

This issue is mostly related to OpenZeppelin libraries.

[https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L57](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L57)

```solidity
ohm.safeApprove(address(MINTR), type(uint256).max);
```

[https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L167](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L167)

```solidity
ohm.safeApprove(address(MINTR), type(uint256).max);
```

### best practice

```solidity
use SafeERC20 for IERC20;

// ...

IERC20(token).safeIncreaseAllowance(spender, value);
```