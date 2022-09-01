## 1.`<ARRAY>.LENGTH` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
### Description
The overheads outlined below are `PER LOOP`, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas) memory arrays use `MLOAD` (3 gas)
calldata arrays use `CALLDATALOAD` (3 gas) Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset
### Instances
// Links to github files
[Governance.sol:L278](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L278)


*Actual codes used*
```
src/policies/Governance.sol:278:        for (uint256 step; step < instructions.length; ) {
```

----
##  2. Comparisons: Boolean comparisons
### Description
Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.
I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false)

### Instances
//Links to github files:
[Governance.sol:L223](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L223)
[Governance.sol:L306](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L306)


*Actual codes used*
```
src/policies/Governance.sol:223:        if (proposalHasBeenActivated[proposalId_] == true) {
src/policies/Governance.sol:306:        if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {
```

----
## 3. Inline a modifier that’s only used once
### Description
As onlyGovernor() is only used once in this contract (in function executeAction()), it should get inlined to save gas:

### Instances
//Links to github files:
[Kernel.sol:L223](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L223)



*Actual codes used*


```
src/Kernel.sol:223:    modifier onlyExecutor() {
```

### Instances where modifiers are used only once
//Links to github files:
[Kernel.sol:L235](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L235)


*Actual codes used*
```
src/Kernel.sol:235:    function executeAction(Actions action_, address target_) external onlyExecutor {
```
----

## 4.++I COSTS LESS GAS COMPARED TO I++ OR I += 1
### Description
*Pre-increments and pre-decrements are cheaper.*

For a `uint256` i variable, the following is true with the Optimizer enabled at 10k:
Increment:
`i += 1` is the most expensive form
`i++` costs `6` `gas` less than `i += 1`
`++i` costs `5 gas` less than `i++` (11 gas less than i += 1)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name post-increment:
### Instances
// Links to Github file
[Operator.sol:L488](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L488)
[Operator.sol:L670](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L670)
[Operator.sol:L686](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L686)
[KernelUtils.sol:L49](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49)
[KernelUtils.sol:L64](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64)


*Actual codes used*
```
src/policies/Operator.sol:488:            decimals++;
src/policies/Operator.sol:670:                _status.low.count++;
src/policies/Operator.sol:686:                _status.high.count++;
src/utils/KernelUtils.sol:49:            i++;
src/utils/KernelUtils.sol:64:            i++;
```

----

## 5. IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED
### Description
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for `(uint256 i = 0; i < numIterations; ++i)` { should be replaced with for `(uint256 i; i < numIterations; ++i) {`

### Instances
// Links to gihthub file


[Kernel.sol:L397](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L397)
[KernelUtils.sol:L43](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L43)
[KernelUtils.sol:L58](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L58)

*Actual codes used*
```
src/Kernel.sol:397:        for (uint256 i = 0; i < reqLength; ) {
src/utils/KernelUtils.sol:43:    for (uint256 i = 0; i < 5; ) {
src/utils/KernelUtils.sol:58:    for (uint256 i = 0; i < 32; ) {
```

----
## 6. Strict inequalities (>) are more expensive than non-strict ones (>=)

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


----
## 7. Use a more recent version of solidity
### Description
Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

### Instances:
//Links to github files:
[IBondCallback.sol:L2](https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L2)
[IOperator.sol:L2](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L2)
[IHeart.sol:L2](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#L2)


*Actual codes used*
```

src/interfaces/IBondCallback.sol:2:pragma solidity >=0.8.0;
src/policies/interfaces/IOperator.sol:2:pragma solidity >=0.8.0;
src/policies/interfaces/IHeart.sol:2:pragma solidity >=0.8.0;
```
----
## 8. Bytes constants are more efficient than string constants
### Descriptions
From the Solidity doc:

If you can limit the length to a certain number of bytes, always use one of bytes1 to bytes32 because they are much cheaper.

Why do Solidity examples use bytes32 type instead of string?

bytes32 uses less gas because it fits in a single word of the EVM, and string is a dynamically sized-type which has current limitations in Solidity (such as can’t be returned from a function to a contract).

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity. Basically, any fixed size variable in solidity is cheaper than variable size. That will save gas on the contract.

Instances of string constant that can be replaced by bytes(1..32) constant :
### Instances:
//Links to github files:
[Governance.sol:L41](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L41)
[Governance.sol:L162](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L162)


*Actual codes used*
```
src/policies/Governance.sol:41:    string proposalURI;
src/policies/Governance.sol:162:        string memory proposalURI_
```