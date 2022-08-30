## Gas Optimizations
 *** 


### G-01: pre-increment `++i/--i` costs less gas than post-increment `i++/i--`
Saves 6 gas per loop in a for loop

Total instances of this issue: 2

instance #1
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49
```solidity
src/utils/KernelUtils.sol

49:            i++;

```

instance #2
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64
```solidity
src/utils/KernelUtils.sol

64:            i++;

```

 *** 


### G-02: Length of the array (`<array>.length`) need not be looked up in every iteration of a for-loop
Reading array length at each iteration of the loop takes total 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the `array.length` saves around `3 gas` per iteration.

Total instances of this issue: 1

instance #1
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L278
```solidity
src/policies/Governance.sol

278:        for (uint256 step; step < instructions.length; ) {

```

 *** 


### G-03: Multiplication or Division by two should use bit shifting
`<x> * 2` is the same as `<x> >> 1` and `<x> / 2` is the same as `<x> >> 1`.
MUL and DIV opcodes cost `2 gas` more than SHL and SHR.

Total instances of this issue: 3

instance #1
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L419
```solidity
src/policies/Operator.sol

419:            uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;

```

instance #2
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L420
```solidity
src/policies/Operator.sol

420:            uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;

```

instance #3
Permalink: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L782-L787
```solidity
src/policies/Operator.sol

782:            capacity =
783:                (capacity.mulDiv(
784:                    10**ohmDecimals * 10**PRICE.decimals(),
785:                    10**reserveDecimals * RANGE.price(true, true)
786:                ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
787:                FACTOR_SCALE;

```

 *** 


### G-04: No need to compare boolean expressions with boolean literals, directly use the expression
if (<x> == true) ==> if (<x>)
if (<x> == false) => if (!<x>)

Total instances of this issue: 2

instance #1
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L223
```solidity
src/policies/Governance.sol

223:        if (proposalHasBeenActivated[proposalId_] == true) {

```

instance #2
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L306
```solidity
src/policies/Governance.sol

306:        if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {

```

 *** 


### G-05: No need to initialize non-constant/non-immutable variables to zero
Since the default value is already zero, overwriting is not required.
Saves `8 gas` per instance.

Total instances of this issue: 3

instance #1
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L397
```solidity
src/Kernel.sol

397:        for (uint256 i = 0; i < reqLength; ) {

```

instance #2
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L43
```solidity
src/utils/KernelUtils.sol

43:    for (uint256 i = 0; i < 5; ) {

```

instance #3
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L58
```solidity
src/utils/KernelUtils.sol

58:    for (uint256 i = 0; i < 32; ) {

```

 *** 


### G-06: Using uints/ints smaller than 256 bits increases overhead
Gas usage becomes higher with uint/int smaller than 256 bits because EVM operates on 32 bytes and uses additional operations to reduce the size from 32 bytes to the target size.

Total instances of this issue: 48

instance #1
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L100
```solidity
src/Kernel.sol

100:    function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}

```

instance #2
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L51
```solidity
src/modules/TRSRY.sol

51:    function VERSION() external pure override returns (uint8 major, uint8 minor) {

```

instance #3
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L25
```solidity
src/modules/MINTR.sol

25:    function VERSION() external pure override returns (uint8 major, uint8 minor) {

```

instance #4
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L115
```solidity
src/modules/RANGE.sol

115:    function VERSION() external pure override returns (uint8 major, uint8 minor) {

```

instance #5
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L27
```solidity
src/modules/PRICE.sol

27:    event MovingAverageDurationChanged(uint48 movingAverageDuration_);

```

instance #6
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L28
```solidity
src/modules/PRICE.sol

28:    event ObservationFrequencyChanged(uint48 observationFrequency_);

```

instance #7
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L44
```solidity
src/modules/PRICE.sol

44:    uint32 public nextObsIndex;

```

instance #8
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L47
```solidity
src/modules/PRICE.sol

47:    uint32 public numObservations;

```

instance #9
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L50
```solidity
src/modules/PRICE.sol

50:    uint48 public observationFrequency;

```

instance #10
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L53
```solidity
src/modules/PRICE.sol

53:    uint48 public movingAverageDuration;

```

instance #11
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L56
```solidity
src/modules/PRICE.sol

56:    uint48 public lastObservationTime;

```

instance #12
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59
```solidity
src/modules/PRICE.sol

59:    uint8 public constant decimals = 18;

```

instance #13
Permalink: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L71-L77
```solidity
src/modules/PRICE.sol

71:    constructor(
72:        Kernel kernel_,
73:        AggregatorV2V3Interface ohmEthPriceFeed_,
74:        AggregatorV2V3Interface reserveEthPriceFeed_,
75:        uint48 observationFrequency_,
76:        uint48 movingAverageDuration_
77:    ) Module(kernel_) {

```

instance #14
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L84
```solidity
src/modules/PRICE.sol

84:        uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();

```

instance #15
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L87
```solidity
src/modules/PRICE.sol

87:        uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();

```

instance #16
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L113
```solidity
src/modules/PRICE.sol

113:    function VERSION() external pure override returns (uint8 major, uint8 minor) {

```

instance #17
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L127
```solidity
src/modules/PRICE.sol

127:        uint32 numObs = numObservations;

```

instance #18
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L185
```solidity
src/modules/PRICE.sol

185:        uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;

```

instance #19
Permalink: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205-L208
```solidity
src/modules/PRICE.sol

205:    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
206:        external
207:        permissioned
208:    {

```

instance #20
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L240
```solidity
src/modules/PRICE.sol

240:    function changeMovingAverageDuration(uint48 movingAverageDuration_) external permissioned {

```

instance #21
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L266
```solidity
src/modules/PRICE.sol

266:    function changeObservationFrequency(uint48 observationFrequency_) external permissioned {

```

instance #22
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L27
```solidity
src/modules/VOTES.sol

27:    function VERSION() external pure override returns (uint8 major, uint8 minor) {

```

instance #23
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L28
```solidity
src/modules/INSTR.sol

28:    function VERSION() public pure override returns (uint8 major, uint8 minor) {

```

instance #24
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L51
```solidity
src/policies/Operator.sol

51:    event CushionFactorChanged(uint32 cushionFactor_);

```

instance #25
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L52
```solidity
src/policies/Operator.sol

52:    event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);

```

instance #26
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L53
```solidity
src/policies/Operator.sol

53:    event ReserveFactorChanged(uint32 reserveFactor_);

```

instance #27
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L54
```solidity
src/policies/Operator.sol

54:    event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);

```

instance #28
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L83
```solidity
src/policies/Operator.sol

83:    uint8 public immutable ohmDecimals;

```

instance #29
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L86
```solidity
src/policies/Operator.sol

86:    uint8 public immutable reserveDecimals;

```

instance #30
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89
```solidity
src/policies/Operator.sol

89:    uint32 public constant FACTOR_SCALE = 1e4;

```

instance #31
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L371
```solidity
src/policies/Operator.sol

371:            int8 priceDecimals = _getPriceDecimals(range.cushion.high.price);

```

instance #32
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L372
```solidity
src/policies/Operator.sol

372:            int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);

```

instance #33
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L418
```solidity
src/policies/Operator.sol

418:            uint8 oracleDecimals = PRICE.decimals();

```

instance #34
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L426
```solidity
src/policies/Operator.sol

426:            int8 priceDecimals = _getPriceDecimals(invCushionPrice);

```

instance #35
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L427
```solidity
src/policies/Operator.sol

427:            int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);

```

instance #36
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L485
```solidity
src/policies/Operator.sol

485:        int8 decimals;

```

instance #37
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L516
```solidity
src/policies/Operator.sol

516:    function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {

```

instance #38
Permalink: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L527-L531
```solidity
src/policies/Operator.sol

527:    function setCushionParams(
528:        uint32 duration_,
529:        uint32 debtBuffer_,
530:        uint32 depositInterval_
531:    ) external onlyRole("operator_policy") {

```

instance #39
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L548
```solidity
src/policies/Operator.sol

548:    function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {

```

instance #40
Permalink: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L559-L563
```solidity
src/policies/Operator.sol

559:    function setRegenParams(
560:        uint32 wait_,
561:        uint32 threshold_,
562:        uint32 observe_
563:    ) external onlyRole("operator_policy") {

```

instance #41
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L665
```solidity
src/policies/Operator.sol

665:        uint32 observe = _config.regenObserve;

```

instance #42
Permalink: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45-L48
```solidity
src/policies/PriceConfig.sol

45:    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
46:        external
47:        onlyRole("price_admin")
48:    {

```

instance #43
Permalink: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L58-L61
```solidity
src/policies/PriceConfig.sol

58:    function changeMovingAverageDuration(uint48 movingAverageDuration_)
59:        external
60:        onlyRole("price_admin")
61:    {

```

instance #44
Permalink: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L69-L72
```solidity
src/policies/PriceConfig.sol

69:    function changeObservationFrequency(uint48 observationFrequency_)
70:        external
71:        onlyRole("price_admin")
72:    {

```

instance #45
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L85
```solidity
src/policies/interfaces/IOperator.sol

85:    function setCushionFactor(uint32 cushionFactor_) external;

```

instance #46
Permalink: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L92-L96
```solidity
src/policies/interfaces/IOperator.sol

92:    function setCushionParams(
93:        uint32 duration_,
94:        uint32 debtBuffer_,
95:        uint32 depositInterval_
96:    ) external;

```

instance #47
Link: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L101
```solidity
src/policies/interfaces/IOperator.sol

101:    function setReserveFactor(uint32 reserveFactor_) external;

```

instance #48
Permalink: https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L109-L113
```solidity
src/policies/interfaces/IOperator.sol

109:    function setRegenParams(
110:        uint32 wait_,
111:        uint32 threshold_,
112:        uint32 observe_
113:    ) external;

```

