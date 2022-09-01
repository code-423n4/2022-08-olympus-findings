

## 1. Pre-increment costs less gas as compared to Post-increment :

++i costs less gas as compared to i++ for unsigned integer, as per-increment is cheaper(its about 5 gas per iteration cheaper)

i++ increments i and returns initial value of i. Which means

```
uint i =  1;
i++; // ==1 but i ==2
```

But ++i returns the actual incremented value:

```
uint i = 1;
++i; // ==2 and i ==2 , no need for temporary variable here
```

In the first case, the compiler has create a temporary variable (when used) for returning 1 instead of 2.

### Instances:
[KernelUtils.sol:L49](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49)
[KernelUtils.sol:L64](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64)
[Operator.sol:L488](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L488)
[Operator.sol:L670](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L670)
[Operator.sol:L686](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L686)
[Operator.sol:L675](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L675)
[Operator.sol:L691](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L691)
```
src/utils/KernelUtils.sol:49:            i++;
src/utils/KernelUtils.sol:64:            i++;
src/policies/Operator.sol:488:            decimals++;
src/policies/Operator.sol:670:                _status.low.count++;
src/policies/Operator.sol:686:                _status.high.count++;
src/policies/Operator.sol:675:                _status.low.count--;
src/policies/Operator.sol:691:                _status.high.count--;
``` 

### Recommendations:
Change post-increment to pre-increment.

-----

## 2. An array's length should be cached to save gas in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

### Instances:
[Governance.sol:L278](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L278)
```
src/policies/Governance.sol:278:        for (uint256 step; step < instructions.length; ) {
``` 


### Remediation:
Here, I suggest storing the array's length in a variable before the for-loop, and use it instead.

-----

## 3. Boolean Comparision

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here

### Instances:
[Governance.sol:L223](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L223)
[Governance.sol:L306](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L306)
```
src/policies/Governance.sol:223:        if (proposalHasBeenActivated[proposalId_] == true) {
src/policies/Governance.sol:306:        if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {
``` 
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-04-fundingonlywhenpricenotflagged-boolean-comparison-147](https://code4rena.com/reports/2022-04-badger-citadel/#g-04-fundingonlywhenpricenotflagged-boolean-comparison-147)


-----

## 4. Strict inequalities (>) are more expensive than non-strict ones (>=)

Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas. I suggest using >= instead of > to avoid some opcodes here:

### Instances:
[RANGE.sol:L245](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L245)
[RANGE.sol:L247](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L247)
[RANGE.sol:L249](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L249)
[Operator.sol:L115](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L115)
[Operator.sol:L254](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L254)
[Operator.sol:L262](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L262)
```
src/modules/RANGE.sol:245:            wallSpread_ > 10000 ||
src/modules/RANGE.sol:247:            cushionSpread_ > 10000 ||
src/modules/RANGE.sol:249:            cushionSpread_ > wallSpread_
src/policies/Operator.sol:115:            configParams[6] > configParams[7] ||
src/policies/Operator.sol:254:                    currentPrice < range.cushion.high.price || currentPrice > range.wall.high.price
src/policies/Operator.sol:262:                    currentPrice > range.cushion.high.price && currentPrice < range.wall.high.price
``` 
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than](https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than)


-----

## 5. x += y costs more gas than x = x + y for state variables

### Instances:
[PRICE.sol:L136](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136)
[PRICE.sol:L222](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L222)
[TRSRY.sol:L96](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L96)
[TRSRY.sol:L97](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L97)
[TRSRY.sol:L131](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131)
[VOTES.sol:L58](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L58)
[Heart.sol:L103](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103)
[BondCallback.sol:L143](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143)
[BondCallback.sol:L144](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L144)
[Governance.sol:L198](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L198)
[Governance.sol:L252](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L252)
[Governance.sol:L254](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L254)
[PRICE.sol:L138](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L138)
[TRSRY.sol:L115](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L115)
[TRSRY.sol:L116](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L116)
[TRSRY.sol:L132](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L132)
[VOTES.sol:L56](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L56)
[Governance.sol:L194](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L194)
```
src/modules/PRICE.sol:136:            _movingAverage += (currentPrice - earliestPrice) / numObs;
src/modules/PRICE.sol:222:            total += startObservations_[i];
src/modules/TRSRY.sol:96:        reserveDebt[token_][msg.sender] += amount_;
src/modules/TRSRY.sol:97:        totalDebt[token_] += amount_;
src/modules/TRSRY.sol:131:        if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;
src/modules/VOTES.sol:58:            balanceOf[to_] += amount_;
src/policies/Heart.sol:103:        lastBeat += frequency();
src/policies/BondCallback.sol:143:        _amountsPerMarket[id_][0] += inputAmount_;
src/policies/BondCallback.sol:144:        _amountsPerMarket[id_][1] += outputAmount_;
src/policies/Governance.sol:198:        totalEndorsementsForProposal[proposalId_] += userVotes;
src/policies/Governance.sol:252:            yesVotesForProposal[activeProposal.proposalId] += userVotes;
src/policies/Governance.sol:254:            noVotesForProposal[activeProposal.proposalId] += userVotes;
src/modules/PRICE.sol:138:            _movingAverage -= (earliestPrice - currentPrice) / numObs;
src/modules/TRSRY.sol:115:        reserveDebt[token_][msg.sender] -= received;
src/modules/TRSRY.sol:116:        totalDebt[token_] -= received;
src/modules/TRSRY.sol:132:        else totalDebt[token_] -= oldDebt - amount_;
src/modules/VOTES.sol:56:        balanceOf[from_] -= amount_;
src/policies/Governance.sol:194:        totalEndorsementsForProposal[proposalId_] -= previousEndorsement;
``` 
### References:

[https://github.com/code-423n4/2022-05-backd-findings/issues/108](https://github.com/code-423n4/2022-05-backd-findings/issues/108)


-----


## 6. Using bools for storage incurs overhead

```
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```

[Refer Here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Instances:
[PRICE.sol:L62](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L62)
[RANGE.sol:L44](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L44)
[Kernel.sol:L113](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L113)
[Kernel.sol:L181](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L181)
[Kernel.sol:L194](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L194)
[Kernel.sol:L197](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L197)
[Heart.sol:L33](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L33)
[BondCallback.sol:L24](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L24)
[Operator.sol:L63](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L63)
[Operator.sol:L66](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L66)
[Operator.sol:L735](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L735)
[Governance.sol:L105](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L105)
[Governance.sol:L117](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L117)

```
src/modules/PRICE.sol:62:    bool public initialized;
src/modules/RANGE.sol:44:        bool active; // Whether or not the side is active (i.e. the Operator is performing market operations on this side, true = active, false = inactive)
src/Kernel.sol:113:    bool public isActive;
src/Kernel.sol:181:    mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions;
src/Kernel.sol:194:    mapping(address => mapping(Role => bool)) public hasRole;
src/Kernel.sol:197:    mapping(Role => bool) public isRole;
src/policies/Heart.sol:33:    bool public active;
src/policies/BondCallback.sol:24:    mapping(address => mapping(uint256 => bool)) public approvedMarkets;
src/policies/Operator.sol:63:    bool public initialized;
src/policies/Operator.sol:66:    bool public active;
src/policies/Operator.sol:735:        bool sideActive = RANGE.active(high_);
src/policies/Governance.sol:105:    mapping(uint256 => bool) public proposalHasBeenActivated;
src/policies/Governance.sol:117:    mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;
``` 
### References:

[https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead](https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead)


-----

## 7. Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed

### Instances:
[PRICE.sol:L59](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59)
[PRICE.sol:L84](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L84)
[PRICE.sol:L87](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L87)
[Operator.sol:L83](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L83)
[Operator.sol:L86](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L86)
[Operator.sol:L418](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L418)
```
src/modules/PRICE.sol:59:    uint8 public constant decimals = 18;
src/modules/PRICE.sol:84:        uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();
src/modules/PRICE.sol:87:        uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();
src/policies/Operator.sol:83:    uint8 public immutable ohmDecimals;
src/policies/Operator.sol:86:    uint8 public immutable reserveDecimals;
src/policies/Operator.sol:418:            uint8 oracleDecimals = PRICE.decimals();
``` 
### References:

[https://code4rena.com/reports/2022-04-phuture#g-14-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead](https://code4rena.com/reports/2022-04-phuture#g-14-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead)


-----

## 8. Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

### Instances:
[IBondAggregator.sol:L2](https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondAggregator.sol#L2)
[IOperator.sol:L2](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L2)
[IHeart.sol:L2](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#L2)
```
src/interfaces/IBondAggregator.sol:2:pragma solidity >=0.8.0;
src/policies/interfaces/IOperator.sol:2:pragma solidity >=0.8.0;
src/policies/interfaces/IHeart.sol:2:pragma solidity >=0.8.0;
``` 
### References:

[https://code4rena.com/reports/2022-06-notional-coop/#10-use-a-more-recent-version-of-solidity](https://code4rena.com/reports/2022-06-notional-coop/#10-use-a-more-recent-version-of-solidity)


-----

## 9. Use Shift Right/Left instead of Division/Multiplication 2X

A division by 2 can be calculated by shifting one to the right.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity’s division operation also includes a division-by-0 prevention which is bypassed using shifting.

I suggest replacing  `/ 2` with  `>> 1` here:

### Instances
[Operator.sol:L372](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L372)
[Operator.sol:L427](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L427)
[Operator.sol:L419](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L419)
[Operator.sol:L420](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L420)
[Operator.sol:L786](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L786)

```
src/policies/Operator.sol:372:            int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
src/policies/Operator.sol:427:            int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
src/policies/Operator.sol:419:            uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
src/policies/Operator.sol:420:            uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
src/policies/Operator.sol:786:                ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
```

### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-32-shift-right-instead-of-dividing-by-2](https://code4rena.com/reports/2022-04-badger-citadel/#g-32-shift-right-instead-of-dividing-by-2)


