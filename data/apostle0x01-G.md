### [G-01] No need to explicitly initialize variables with default values

### Impact
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

#### Findings:
```
src/Kernel.sol::397 => for (uint256 i = 0; i < reqLength; ) {
src/utils/KernelUtils.sol::43 => for (uint256 i = 0; i < 5; ) {
src/utils/KernelUtils.sol::58 => for (uint256 i = 0; i < 32; ) {
```

### [G-02] Cache Array Length Outside of Loop

#### Impact
An array's length should be cached to save gas in for-loops
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

Here, I suggest storing the array's length in a variable before the for-loop, and use it instead:

#### Findings:
```
src/policies/Governance.sol::278 => for (uint256 step; step < instructions.length; ) {
```

### [G-03] Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

#### Findings:
```
src/policies/Operator.sol::372 => int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
src/policies/Operator.sol::419 => uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
src/policies/Operator.sol::420 => uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
src/policies/Operator.sol::427 => int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
src/policies/Operator.sol::786 => ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
```
Reference: https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible
