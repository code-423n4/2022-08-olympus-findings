

## USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract�s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed


https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L100


```
    function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L51


```
    function VERSION() external pure override returns (uint8 major, uint8 minor) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/MINTR.sol#L25


```
    function VERSION() external pure override returns (uint8 major, uint8 minor) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L45


```
        uint48 lastActive; // Unix timestamp when the side was last active (in seconds)
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L85


```
                lastActive: uint48(block.timestamp),
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L92


```
                lastActive: uint48(block.timestamp),
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L115


```
    function VERSION() external pure override returns (uint8 major, uint8 minor) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L136


```
                _range.high.lastActive = uint48(block.timestamp);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L148


```
                _range.low.lastActive = uint48(block.timestamp);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L191


```
                lastActive: uint48(block.timestamp),
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L200


```
                lastActive: uint48(block.timestamp),
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L27


```
    event MovingAverageDurationChanged(uint48 movingAverageDuration_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L28


```
    event ObservationFrequencyChanged(uint48 observationFrequency_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L44


```
    uint32 public nextObsIndex;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L47


```
    uint32 public numObservations;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L50


```
    uint48 public observationFrequency;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L53


```
    uint48 public movingAverageDuration;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L56


```
    uint48 public lastObservationTime;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L59


```
    uint8 public constant decimals = 18;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L75


```
        uint48 observationFrequency_,
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L76


```
        uint48 movingAverageDuration_
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L84


```
        uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L87


```
        uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L97


```
        numObservations = uint32(movingAverageDuration_ / observationFrequency_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L113


```
    function VERSION() external pure override returns (uint8 major, uint8 minor) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L127


```
        uint32 numObs = numObservations;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L143


```
        lastObservationTime = uint48(block.timestamp);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L185


```
        uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L215


```
        if (startObservations_.length != numObs || lastObservationTime_ > uint48(block.timestamp))
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L240


```
    function changeMovingAverageDuration(uint48 movingAverageDuration_) external permissioned {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L257


```
        numObservations = uint32(newObservations);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L266


```
    function changeObservationFrequency(uint48 observationFrequency_) external permissioned {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L289


```
        numObservations = uint32(newObservations);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L27


```
    function VERSION() external pure override returns (uint8 major, uint8 minor) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L28


```
    function VERSION() public pure override returns (uint8 major, uint8 minor) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L51


```
    event CushionFactorChanged(uint32 cushionFactor_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L52


```
    event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L53


```
    event ReserveFactorChanged(uint32 reserveFactor_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L54


```
    event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L83


```
    uint8 public immutable ohmDecimals;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L86


```
    uint8 public immutable reserveDecimals;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L89


```
    uint32 public constant FACTOR_SCALE = 1e4;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L97


```
        uint32[8] memory configParams // [cushionFactor, cushionDuration, cushionDebtBuffer, cushionDepositInterval, reserveFactor, regenWait, regenThreshold, regenObserve]
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L106


```
        if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L108


```
        if (configParams[3] < uint32(1 hours) || configParams[3] > configParams[1])
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L116


```
            configParams[7] == uint32(0)
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L127


```
            count: uint32(0),
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L128


```
            lastRegen: uint48(block.timestamp),
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L129


```
            nextObservation: uint32(0),
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L210


```
            uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L216


```
            uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L377


```
                uint8(
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L403


```
                vesting: uint48(0), // Instant swaps
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L404


```
                conclusion: uint48(block.timestamp + config_.cushionDuration),
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L418


```
            uint8 oracleDecimals = PRICE.decimals();
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L432


```
                uint8(
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L455


```
                vesting: uint48(0), // Instant swaps
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L456


```
                conclusion: uint48(block.timestamp + config_.cushionDuration),
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L516


```
    function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L528


```
        uint32 duration_,
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L529


```
        uint32 debtBuffer_,
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L530


```
        uint32 depositInterval_
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L535


```
        if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L536


```
        if (depositInterval_ < uint32(1 hours) || depositInterval_ > duration_)
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L548


```
    function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L560


```
        uint32 wait_,
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L561


```
        uint32 threshold_,
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L562


```
        uint32 observe_
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L665


```
        uint32 observe = _config.regenObserve;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L705


```
            _status.high.count = uint32(0);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L707


```
            _status.high.nextObservation = uint32(0);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L708


```
            _status.high.lastRegen = uint48(block.timestamp);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L717


```
            _status.low.count = uint32(0);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L719


```
            _status.low.nextObservation = uint32(0);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L720


```
            _status.low.lastRegen = uint48(block.timestamp);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L58


```
    function changeMovingAverageDuration(uint48 movingAverageDuration_)
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L69


```
    function changeObservationFrequency(uint48 observationFrequency_)
```
            

## USING BOOLS FOR STORAGE INCURS OVERHEAD

// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) 
for the extra SLOAD, and to avoid Gsset (20000 gas) 
when changing from �false� to �true�, 
after having been �true� in the past


https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L113


```
    bool public isActive;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L126


```
    function setActiveStatus(bool activate_) external onlyKernel {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L181


```
    mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L194


```
    mapping(address => mapping(Role => bool)) public hasRole;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L197


```
    mapping(Role => bool) public isRole;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L207


```
        bool granted_
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L394


```
        bool grant_
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L20


```
    event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L21


```
    event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L22


```
    event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L23


```
    event CushionDown(bool high_, uint256 timestamp_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L44


```
        bool active; // Whether or not the side is active (i.e. the Operator is performing market operations on this side, true = active, false = inactive)
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L127


```
    function updateCapacity(bool high_, uint256 capacity_) external permissioned {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L184


```
    function regenerate(bool high_, uint256 capacity_) external permissioned {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L216


```
        bool high_,
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L281


```
    function capacity(bool high_) external view returns (uint256) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L291


```
    function active(bool high_) external view returns (bool) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L302


```
    function price(bool wall_, bool high_) external view returns (uint256) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L320


```
    function spread(bool wall_) external view returns (uint256) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L330


```
    function market(bool high_) external view returns (uint256) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L340


```
    function lastActive(bool high_) external view returns (uint256) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L62


```
    bool public initialized;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L45


```
    function transfer(address to_, uint256 amount_) public pure override returns (bool) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L55


```
    ) public override permissioned returns (bool) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L63


```
    bool public initialized;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L66


```
    bool public active;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L130


```
            observations: new bool[](configParams[7])
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L363


```
    function _activate(bool high_) internal {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L473


```
    function _deactivate(bool high_) internal {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L576


```
        _status.high.observations = new bool[](observe_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L580


```
        _status.low.observations = new bool[](observe_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L618


```
    function regenerate(bool high_) external onlyRole("operator_admin") {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L634


```
    function _updateCapacity(bool high_, uint256 reduceBy_) internal {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L699


```
    function _regenerate(bool high_) internal {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L706


```
            _status.high.observations = new bool[](_config.regenObserve);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L718


```
            _status.low.observations = new bool[](_config.regenObserve);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L732


```
    function _checkCushion(bool high_) internal {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L735


```
        bool sideActive = RANGE.active(high_);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L778


```
    function fullCapacity(bool high_) public view override returns (uint256) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L24


```
    mapping(address => mapping(uint256 => bool)) public approvedMarkets;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L33


```
    bool public active;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L89


```
    event WalletVoted(uint256 proposalId, address voter, bool for_, uint256 userVotes);
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L105


```
    mapping(uint256 => bool) public proposalHasBeenActivated;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L117


```
    mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L240


```
    function vote(bool for_) external {
```
            

## IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED


https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L397


```
        for (uint256 i = 0; i < reqLength; ) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L43


```
    for (uint256 i = 0; i < 5; ) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L58


```
    for (uint256 i = 0; i < 32; ) {
```
            
   
## function can be marked as external instead of public


https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L439


```
    function grantRole(Role role_, address addr_) public onlyAdmin {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L451


```
    function revokeRole(Role role_, address addr_) public onlyAdmin {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L75


```
    function withdrawReserves(
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/MINTR.sol#L33


```
    function mintOhm(address to_, uint256 amount_) public permissioned {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/MINTR.sol#L37


```
    function burnOhm(address from_, uint256 amount_) public permissioned {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L215


```
    function updateMarket(
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L51


```
    function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L145


```
    function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {
```
            

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L151


```
    function getActiveProposal() public view returns (ActivatedProposal memory) {
```
            

## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset


https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L278


```
        for (uint256 step; step < instructions.length; ) {
```
            