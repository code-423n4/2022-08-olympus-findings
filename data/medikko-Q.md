### Unsafe ERC20 Operation(s)

#### Impact

Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:

```
2022-08-olympus/src/policies/Governance.sol::259 => VOTES.transferFrom(msg.sender, address(this), userVotes);
2022-08-olympus/src/policies/Governance.sol::312 => VOTES.transferFrom(address(this), msg.sender, userVotes);
```



### Unspecific Compiler Version Pragma

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

_There are **4** instances of this issue:_


#### Findings:

```solidity
File: ./src/policies/interfaces/IHeart.sol

2:      pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IHeart.sol#L2

```solidity
File: ./src/policies/interfaces/IOperator.sol

2:      pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IOperator.sol#L2

### Do not use Deprecated Library Functions

#### Impact

The usage of deprecated library functions should be discouraged.

This issue is mostly related to OpenZeppelin libraries.

_There are **2** instances of this issue:_

```solidity
File: ./src/policies/BondCallback.sol

57:     ohm.safeApprove(address(MINTR), type(uint256).max);
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L57

```solidity
File: ./src/policies/Operator.sol

167:    ohm.safeApprove(address(MINTR), type(uint256).max);
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L167
