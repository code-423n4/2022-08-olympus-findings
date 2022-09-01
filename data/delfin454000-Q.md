
### Typos

[PRICE.sol: L126](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L126)
```solidity
        // Cache numbe of observations to save gas.
```
Change `numbe` to `number`
___
The same typo (`deactive`) occurs in both lines referenced below:

[Operator.sol: L295](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L295)

[Operator.sol: L326](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L326)

```solidity
            /// If wall is down after swap, deactive the cushion as well
```
Change `deactive` to `deactivate` in both cases
___
___

### Long single line comments 
In theory, comments over 79 characters should wrap using multi-line comment syntax. Even if somewhat longer comments are acceptable and a scroll bar is provided, very long comments can interfere with readability. Some of the long comments in `Olympus` do wrap (e.g., [PRICE.sol: L237-239](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L237-L239)) — the treatment of very long comments is inconsistent and should be made consistent. Below is an example of a line that should wrap:

[PRICE.sol: L31](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L31)
```solidity
    /// @dev    Price feeds. Chainlink typically provides price feeds for an asset in ETH. Therefore, we use two price feeds against ETH, one for OHM and one for the Reserve asset, to calculate the relative price of OHM in the Reserve asset.
```
Suggestion:
```solidity
    /// @dev    Price feeds. Chainlink typically provides price feeds for an asset in ETH. 
    ///         Therefore, we use two price feeds against ETH — one for OHM and one for the
    ///         Reserve asset — to calculate the relative price of OHM in the Reserve asset.
```
Similarly for the following 10 examples:

[RANGE.sol: L40](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L40)

[RANGE.sol: L44](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L44)

[RANGE.sol: L46](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L46)

[RANGE.sol: L47](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L47)

[RANGE.sol: L61](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L61)

[PRICE.sol: L120](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L120)

[Operator.sol: L657](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L657)

[IOperator.sol: L15](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IOperator.sol#L15)

[IOperator.sol: L90](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IOperator.sol#L90)

[IOperator.sol: L91](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IOperator.sol#L91)
___
___


### Comments concerning unfinished work or open items should be addressed
Comments that contain TODOs or other language referring to open items should be addressed and modified or removed. Below are two instances:
___
[TreasuryCustodian.sol: L50-56](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L50-L56)
```solidity
    // Anyone can call to revoke a deactivated policy's approvals.
    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
    // TODO must reorg policy storage to be able to check for deactivated policies.
    function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {
        if (Policy(policy_).isActive()) revert PolicyStillActive();

        // TODO Make sure `policy_` is an actual policy and not a random address.
```
___
[Operator.sol: L656-659](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L656-L659)
```solidity
        /// Get latest price
        /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
        /// Current price is guaranteed to be up to date, but may be a bad value if not checked?
        uint256 currentPrice = PRICE.getLastPrice();
```
___
___


### Update sensitive terms in both comments and code
Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/). Example: 

[Operator.sol: L411-412](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L411-L412)
```solidity
            /// Whitelist the bond market on the callback
            callback.whitelist(address(auctioneer.getTeller()), market);
```
Suggestion: Change `whitelist` to 'allowlist` 
___

Similarly for the following six instances (change `whitelisted` to `allowlisted` where it occurs):

[Operator.sol: L463-464](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L463-L464)

[BondCallback.sol: L83-87](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L83-L87)

[BondCallback.sol: L105](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L105)

[IBondCallback.sol: L8](https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L8)

[IBondCallback.sol: L26](https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L26)

[IBondCallback.sol: L30](https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L30)
___
___


### Use consistent initialization of counters in `for` loops 
Some `for` loop counters in the contracts are initiated to zero (`uint256 i = 0;`) whereas others are not (`uint256 i;`). It is not necessary to initialize `for` loop counters to zero since this is their default value. For consistency, it makes sense to omit such initializations in the `for` loops below: 
___
[Kernel.sol: L397-406](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L397-L406)

[KernelUtils.sol: L43-51](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L43-L51)

[KernelUtils.sol: L58-66](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L58-L66)
___
___
