### Use defualt value rather than overwriite variable with their default value.

Overriting varibles with defualt values with their default value will waste only gas and not necessary.

_There are **3** instances of this issue:_

#### Findings:

```solidity
File: ./src/Kernel.sol
397:    for (uint256 i = 0; i < reqLength; ) {
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L397

```solidity
File: ./src/utils/KernelUtils.sol
43:     for (uint256 i = 0; i < 5; ) {

58:     for (uint256 i = 0; i < 32; ) {
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L43

### In for loop use outside variable for array length

For loop written like this`for (uint256 i; i < array.length; ++i) {` will cost more gas than `for (uint256 i; i < _lengthOfArray; ++i) {` because for every iteration we use mload and memory_offset
that will cost about 6 gas

_There are **2** instances of this issue:_

#### Findings:

```solidity
File: ./src/policies/Governance.sol

278: for (uint256 step; step < instructions.length; ) {
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L278

```solidity
File: ./src/utils/KernelUtils.sol

58:     for (uint256 i = 0; i < 32; ) {
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L58

### `++i`/`--i` are more cheap operations than `i++`/`i--`

Using a `++i`/`--i` operations can save about 6 gas for loop/instance because compiler will make less operations

_There are **2** instances of this issue:_

```solidity
File: ./src/utils/KernelUtils.sol

49:     i++;

64:     i++;
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L49

### If you use bit shifting will save some gas

Use of bit shifting operations are more cheap than normal multiplication/division operations. `MUL` and `DIV` costs 5 gas rather than `SHL` and `SHR` that costs 3 gas. You can use them where is possible

_There are **3** instances of this issue:_

#### Findings:

```
File: ./src/policies/Operator.sol

372:    int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);

419:    uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;

420:    uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;

427:    int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);

786:    ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L372