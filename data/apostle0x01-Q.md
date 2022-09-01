### [L-01] Unsafe ERC20 Operation(s)

#### Impact
ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.
Use `safetransferFrom` (using OpenZeppelin's SafeERC20) instead of `transferFrom`
#### Findings:
```
src/policies/Governance.sol::259 => VOTES.transferFrom(msg.sender, address(this), userVotes);
src/policies/Governance.sol::312 => VOTES.transferFrom(address(this), msg.sender, userVotes);
```


### [L-02] Do not use Deprecated Library Functions

#### Impact
The usage of deprecated library functions should be discouraged.

This issue is mostly related to OpenZeppelin libraries.
Use `safeIncreaseAllowance` instead of `safeApprove`
#### Findings:
```
src/policies/BondCallback.sol::57 => ohm.safeApprove(address(MINTR), type(uint256).max);
src/policies/Operator.sol::167 => ohm.safeApprove(address(MINTR), type(uint256).max);
```
Reference: https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l005---do-not-use-deprecated-library-functions
