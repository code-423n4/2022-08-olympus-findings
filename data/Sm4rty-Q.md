
## 1. _safeMint() should be used rather than _mint() wherever possible

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

### Instances
[VOTES.sol:L36](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L36)
```
src/modules/VOTES.sol:36:        _mint(wallet_, amount_);
``` 
### Recommendations:

Use _safeMint() instead of _mint().

-----

## 2. Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

### Instances:
[Operator.sol:L657](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L657)
[TreasuryCustodian.sol:L51](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L51)
[TreasuryCustodian.sol:L52](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L52)
[TreasuryCustodian.sol:L56](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L56)
```
src/policies/Operator.sol:657:        /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
src/policies/TreasuryCustodian.sol:51:    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
src/policies/TreasuryCustodian.sol:52:    // TODO must reorg policy storage to be able to check for deactivated policies.
src/policies/TreasuryCustodian.sol:56:        // TODO Make sure `policy_` is an actual policy and not a random address.
``` 
### References:

[https://code4rena.com/reports/2022-05-sturdy/#l-09-open-todos](https://code4rena.com/reports/2022-05-sturdy/#l-09-open-todos)

-----


### 3. safeApprove() is Deprecated.

Deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead

Using this deprecated function can lead to unintended reverts and potentially the locking of funds. A deeper discussion on the deprecation of this function is in OZ issue [#2219](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2219). The OpenZeppelin ERC20 safeApprove() function has been deprecated, as seen in the comments of the OpenZeppelin code.

### Instances:
[BondCallback.sol:L57](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L57)
[Operator.sol:L167](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L167)
```
src/policies/BondCallback.sol:57:        ohm.safeApprove(address(MINTR), type(uint256).max);
src/policies/Operator.sol:167:        ohm.safeApprove(address(MINTR), type(uint256).max);
``` 
### References:

[https://code4rena.com/reports/2022-02-hubble/#l-07-deprecated-safeapprove-function](https://code4rena.com/reports/2022-02-hubble/#l-07-deprecated-safeapprove-function)


-----

## 4. Variable names that consist of all capital letters should be reserved for const/immutable variables

If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like this).

### Instances:
https://github.com/code-423n4/2022-08-olympus/blob/main/
https://github.com/code-423n4/2022-08-olympus/blob/main/
https://github.com/code-423n4/2022-08-olympus/blob/main/
```
src/policies/Heart.sol:45:    OlympusPrice internal PRICE;
src/policies/PriceConfig.sol:11:    OlympusPrice internal PRICE;
src/policies/Operator.sol:69:    OlympusPrice internal PRICE;
```

### References:

[https://code4rena.com/reports/2022-05-sturdy#n-08-variable-names-that-consist-of-all-capital-letters-should-be-reserved-for-constimmutable-variables](https://code4rena.com/reports/2022-05-sturdy#n-08-variable-names-that-consist-of-all-capital-letters-should-be-reserved-for-constimmutable-variables)
