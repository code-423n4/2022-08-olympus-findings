
## 1. ++i costs less gas compared to i++ or i += 1, same for --i/i--. Especially in for loops

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments i and returns the initial value of `i`.

```
uint i = 1;  
i++; // == 1 but i == 2
```
But ++i returns the actual incremented value:
```
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

I suggest using ++i instead of i++ to increment the value of an uint variable.

If done inside for loop, saves 6 gas per loop.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol
```
49:         i++;
64:         i++;
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol
```
488:        decimals++;
670:        _status.low.count++;
675:        _status.low.count--;
686:        _status.high.count++;
691:        _status.high.count--;
```

## 2. Use a more recent version of solidity

- Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath  
- Use a solidity version of at least 0.8.2 to get compiler automatic inlining  
- Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads  
- Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings  
- Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value


https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol 
```
2:			pragma solidity >=0.8.0;
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol
```
2:			pragma solidity >=0.8.0;
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol
```
2:			pragma solidity >=0.8.0;	
```

## 3. No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: `for (uint256 i = 0; i < reqLength;) {` should be replaced with for `(uint256 i; i < reqLength;) {`

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol
```
 397:       for (uint256 i = 0; i < reqLength; ) {
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol
```
43:  	    for (uint256 i = 0; i < 5; ) {
58:		    for (uint256 i = 0; i < 32; ) {
```

## 4. \<x\> += \<y\> costs more gas than \<x\> = \<x\> + \<y\> for state variables

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol
```
96:         reserveDebt[token_][msg.sender] += amount_;
97:         totalDebt[token_] += amount_;
131:        if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol
```
136:        _movingAverage += (currentPrice - earliestPrice) / numObs;
222:        total += startObservations_[i];
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol
```
58:          balanceOf[to_] += amount_;
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol
```
143:        _amountsPerMarket[id_][0] += inputAmount_;
144:        _amountsPerMarket[id_][1] += outputAmount_;
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol
```
103:        lastBeat += frequency();
```
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol
```
198:        totalEndorsementsForProposal[proposalId_] += userVotes;
252:        yesVotesForProposal[activeProposal.proposalId] += userVotes;
254:        noVotesForProposal[activeProposal.proposalId] += userVotes;
```

## 5. \<array>.length should not be looked up in every loop of a for-loop

The overheads outlined below are PER LOOP, excluding the first loop

- storage arrays incur a Gwarmaccess (100 gas)
- memory arrays use MLOAD (3 gas)
- calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP\<N> (3 gas), and gets rid of the extra DUP\<N> needed to store the stack offset

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol
```
279:        for (uint256 step; step < instructions.length; ) {
```

## 6. Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(directValue) instead of if(directValue == true)

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol
```
223:        if (proposalHasBeenActivated[proposalId_] == true) {
306:        if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {
```

## 7 . Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

There are uint8, uint32, uint48 in almost all contracts in scope, they should all be checked and if possible use uint/int.
Example:
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol - 31, 32, 33
```        
31: 		uint32 count; // current number of price points that count towards regeneration
32:         uint48 lastRegen; // timestamp of the last regeneration
33:         uint32 nextObservation; // index of the next observation in the observations array
```

## 8. Using bools for storage incurs overhead

// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and 
// pointer aliasing, and it cannot be disabled.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

Use uint256(1) and uint256(2) for true/false

All contracts should be checked and if possible avoid using uint instead of bools
Example:
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol - 113, 181, 197
```
113:    bool public isActive;
181:    mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions;
197:    mapping(Role => bool) public isRole;
```

## 9. Not using the named return variables when a function returns, wastes deployment gas

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol
```
493:        return decimals - int8(PRICE.decimals());
```

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol
```
122:        return uint256(PRICE.observationFrequency());
```

## 10. Multiplication/division by two should use bit shifting


\<x> * 2 is equivalent to \<x> << 1 and \<x> / 2 is the same as \<x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol - 372, 419, 420, 427, 786
```
372:            int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
419:            uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
420:            uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
427:            int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
786:            ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
```

