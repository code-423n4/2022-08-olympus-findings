
# GAS
## Comparision with a boolean
### Summary
There are a number of instances where a boolean variable/function is checked. This check can be further simplified from `variable == true` to `!variable`.

### Github Permalink
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L205-L236
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L295-L313
### Mitigation
Simplify boolean comparisons in order to improve readility and save gas

## Public function visibility can be made external
### Summary
 Functions should have the strictest visibility possible. Public functions may lead to more gas usage by forcing the copy of their parameters to memory from calldata.
### Details
 If a function is never called from the contract it should be marked as external. This will save gas.
### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L215-L235
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/MINTR.sol#L33-L35
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/MINTR.sol#L37-L39
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol#L28-L30
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L145-L147
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L151-L153
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol#L37-L39
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L75-L85
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L451-L458
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L439-L448

### Mitigation
 Consider changing visibility from public to external.

## Caching calculation result can save gas
### Summary
Reading from state is an expensive operation, it should be avoided when able to.
### Details
In this context, value is already known as it is calculated in the same function, but in the emit what is emitted is not the local calculation but the state variable that stores result.
```
        // Calculate new moving average
        if (currentPrice > earliestPrice) {
            _movingAverage += (currentPrice - earliestPrice) / numObs;
        } else {
            _movingAverage -= (earliestPrice - currentPrice) / numObs;
        }
        //@audit _movingAverage can be calculated to a local variable and then assigned to state so the local variable is read rather than the state in the emit

        // Push new observation into storage and store timestamp taken at
        observations[nextObsIndex] = currentPrice;
        lastObservationTime = uint48(block.timestamp);
        nextObsIndex = (nextObsIndex + 1) % numObs; 
        
        emit NewObservation(block.timestamp, currentPrice, _movingAverage);
    }
```
### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L134-L147

Same scenario here with all 4 variables in the emit
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L164-L177

### Mitigation
Consider a local variable for avoiding unnecessary reads.

## Variables should be cached when used several times
### Summary
Variables read more than once improves gas usage when cached into local variable
### Details
In loops or state variables, this is even more gas saving

### Github Permalinks
activeProposal.proposalId
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L242-L262
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L266-L285

reward
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Heart.sol#L112-L113

### Mitigation
Cache variables used more than one into a local variable.



## usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
### Summary
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### Details
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 
Use a larger size than downcast where needed

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L44-L59
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L83
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L86
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L89
### Mitigation
Consider using some data type that uses 32 bytes, for example uint256

## Using bools for storage incurs overhead { 
### Summary
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

### Details
Here is one example of OpenZeppelin about this optimization
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Github Permalinks
- variables
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L113
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L207
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L394
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L62
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L216
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Heart.sol#L33
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L63
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L66
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L735
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L48

- functions/events
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L128
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L121
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAggregator.sol#L74
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L143
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L131
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L778
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L732
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L699
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L634
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L618
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L473
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L363
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L240
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L89
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L340
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L330
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L320
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L302
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L291
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L281
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L184
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L127
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L23
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L22
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L21
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L20
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L126
### Mitigation
Consider using uint256 with values 0 and 1 rather than bool



## Pack structs tightly
### Summary
Gas efficiency can be achieved by tightly packing the struct. Struct variables are stored in 32 bytes each so you can group smaller types to occupy less storage. 

### Details
You can read more here: https://fravoll.github.io/solidity-patterns/tight_variable_packing.html or in the official documentation: https://docs.soliditylang.org/en/v0.4.21/miscellaneous.html
### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/interfaces/IOperator.sol#L30-L35

### Mitigation
Order structs to reduce gas usage.

## Store using Struct over multiple mappings
### Summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L168
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L174-L181
- - - 
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/external/OlympusERC20.sol#L666-L669
- - -
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L35-L39
- - -
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L95-L117

### Mitigation
Consider mixing different mappings into an struct when able in order to save gas.


## Using private rather than public for constants saves gas
### Summary
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L59
    `uint8 public constant decimals = 18;`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L65
    `uint256 public constant FACTOR_SCALE = 1e4;`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L121
    `uint256 public constant SUBMISSION_REQUIREMENT = 100;`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L124
    `uint256 public constant ACTIVATION_DEADLINE = 2 weeks;`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L127
    `uint256 public constant GRACE_PERIOD = 1 weeks;`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L130
    `uint256 public constant ENDORSEMENT_THRESHOLD = 20;`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L133
    `uint256 public constant EXECUTION_THRESHOLD = 33;`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L137
    `uint256 public constant EXECUTION_TIMELOCK = 3 days;`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L89
    `uint32 public constant FACTOR_SCALE = 1e4;`

### Mitigation
Consider replacing public for private in constants for gas saving.


## Index initialized in for loop
### Summary
In for loops is not needed to initialize indexes to 0 as it is the default uint/int value. This saves gas.

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L397
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/utils/KernelUtils.sol#L43
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/utils/KernelUtils.sol#L58


### Mitigation
Don't initialize variables to default value


## ++i costs less gas compared to i++, the same happens with --i and i-- 
### Summary
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). 
This statement is true even with the optimizer enabled. 

### Details
i++ increments i and returns the initial value of i . 
Which means:
uint i = 1;
i++; // == 1 but i == 2

But ++i returns the actual incremented value:

uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

### Github Permalinks
var++
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/utils/KernelUtils.sol#L49
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/utils/KernelUtils.sol#L64
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L488
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L670
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L686

var--
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L675
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L691


### Mitigation
Replace to ++i or --i as needed.



## <array>.length should no be looked up in every loop of a for-loop
### Summary
In loops not assigning the length to a variable so memory accessed a lot (caching local variables)

### Details
The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L278

### Mitigation
Assign the length of the array.length to a local variable in loops for gas savings




## Shift right instead of dividing by 2
### Summary
Shifting is cheaper than dividing by 2

### Details
A division by 2 can be calculated by shifting one to the right.
While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity’s division operation also includes a division-by-0 prevention which is bypassed using shifting.

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L372
`int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L427
`int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/interfaces/IBondAuctioneer.sol#L41
`/// @dev                        Should be calculated as: (payoutDecimals - quoteDecimals) - ((payoutPriceDecimals - quotePriceDecimals) / 2)`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L419
`uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L420
`uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L786
`) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /`

### Mitigation
Consider replacing / 2 with >> 1 here


## Internal functions only called once can be inlined to save gas
### Summary
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.
### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Heart.sol#L111-L114
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Operator.sol#L652
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L409
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L378
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L351
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L325
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L295
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L279
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L266

### Mitigation
Consider changing internal function only called once to inline code for gas savings



## Functions guaranteed to revert when called by normal users can be marked payable
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L439
`function grantRole(Role role_, address addr_) public onlyAdmin {`

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/Kernel.sol#L451
`function revokeRole(Role role_, address addr_) public onlyAdmin {`

### Mitigation
It's suggested to add payable to functions guaranteed to revert when called by normal users to improve gas costs


## >= cheaper than >
### Summary
Strict inequalities ( > ) are more expensive than non-strict ones ( >= ). This is due to some supplementary checks (ISZERO, 3 gas)

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L247
`if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {`

### Mitigation
Consider using >= 1 instead of > 0 to avoid some opcodes


## <X> += <Y> costs more gas than <X> = <X> + <Y> for state variables
### Summary
x+=y costs more gas than x=x+y for state variables

### Github Permalinks
- +=
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L136

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L96

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L97

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L131

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/VOTES.sol#L58

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/BondCallback.sol#L143

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/BondCallback.sol#L144

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L198

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L252

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L254

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Heart.sol#L103



- Same as += with state variables but -= 
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L138

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L115

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L116

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L132

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/VOTES.sol#L56

https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L194

### Mitigation
Don't use += for state variables as it cost more gas.

## Use calldata instead of memory for function parameters
### Summary
It is generally cheaper to load variables directly from calldata, rather than copying them to memory. 
### Details
Only use memory if the variable needs to be modified
### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L205
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/BondCallback.sol#L152
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L162
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/Governance.sol#L162
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/PriceConfig.sol#L45
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/TreasuryCustodian.sol#L53
### Mitigation
Use calldata rather than memory in external functions where the parameter is not modified but only read

## Unused named returns
### Summary
Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity 
### Details
Also as returns variable is ignored, it wastes extra gas

### Github Permalinks
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/INSTR.sol#L29
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/MINTR.sol#L25-L27
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/PRICE.sol#L113-L115
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/RANGE.sol#L115-L117
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/TRSRY.sol#L51-L53
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/modules/VOTES.sol#L26-L29
https://github.com/code-423n4/2022-08-olympus/blob/549b96bcf8b97807738572605f6b1e26b33ef411/src/policies/BondCallback.sol#L177
### Mitigation
Remove return or returns when both used

