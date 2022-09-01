# [G-01] Prefix increment costs less gas than postfix increment

There are 7 instances of this issue.

```
File: src/policies/Operator.sol
488: decimals++;
670: _status.low.count++;
675: _status.low.count--;
686: _status.high.count++;
691: _status.high.count--;
```

```
File: src/utils/KernelUtils.sol
49: i++;
64: i++;
```

# [G-02] Cache the length of the array before the loop

There is 1 instance of this issue.

```
File: src/policies/Governance.sol
278: for (uint256 step; step < instructions.length; ) {
```

# [G-03] Initializing a variable with the default value wastes gas

There are 3 instances of this issue.

```
File: src/Kernel.sol
397: for (uint256 i = 0; i < reqLength; ) {
```

```
File: src/utils/KernelUtils.sol
43: for (uint256 i = 0; i < 5; ) {
58: for (uint256 i = 0; i < 32; ) {
```

# [G-04] Use != 0 instead of > 0 to save gas.

Replace `> 0` with `!= 0` for unsigned integers.

On the instance bellow `userVotesForProposal` is a nested mapping of uints.

```
File: src/policies/Governance.sol
247: if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
```

# [G-05] Use right/left shift instead of division/multiplication to save gas

There are 5 instances of this issue.

```
File: src/policies/Operator.sol
372: int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
419: uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
420: uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
427: int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
786: ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
```

# [G-06] Don’t compare boolean expressions to boolean literals

There are 2 instances of this issue.

```
File: src/policies/Governance.sol
223: if (proposalHasBeenActivated[proposalId_] == true) {
306: if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {
```

# [G-07] x += y costs more gas than x = x + y for state variables

There is 1 instance of this issue in `Price.sol` and 2 instances in `TRSRY.sol`

```
File: src/modules/PRICE.sol
136: _movingAverage += (currentPrice - earliestPrice) / numObs;
```

```
File: main/src/modules/TRSRY.sol
96: reserveDebt[token_][msg.sender] += amount_;
97: totalDebt[token_] += amount_;
```

# [G-08] Using private rather than public for constants, saves gas

The values can still be inspected on the source code if necessary.

There are 9 instances of this issue.

```
File: src/modules/PRICE.sol
59: uint8 public constant decimals = 18;
```

```
File: src/modules/RANGE.sol
65: uint256 public constant FACTOR_SCALE = 1e4;
```

```
File: src/policies/Governance.sol
121: uint256 public constant SUBMISSION_REQUIREMENT = 100;
124: uint256 public constant ACTIVATION_DEADLINE = 2 weeks;
127: uint256 public constant GRACE_PERIOD = 1 weeks;
130: uint256 public constant ENDORSEMENT_THRESHOLD = 20;
133: uint256 public constant EXECUTION_THRESHOLD = 33;
137: uint256 public constant EXECUTION_TIMELOCK = 3 days;
```

```
File: src/policies/Operator.sol
89: uint32 public constant FACTOR_SCALE = 1e4;
```
