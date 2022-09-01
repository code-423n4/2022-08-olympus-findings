## Table of Contents
Total of 11 Issues Found.
- Storage Variables can be Packed into Fewer Storage Slots
- Unchanging State Variable Should be Immutable
- Change Function Visibility Public to External
- Internal Function Called Only Once can be Inlined
- Use Bit Shifting Instead of Multiplication/Division of 2
- Use Calldata instead of Memory for Read Only Function Parameters
- Boolean Comparisons
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- Unnecessary Default Value Initialization
- Store Array's Length as a Variable
- ++i Costs Less Gas than i++

&ensp;
### Storage Variables can be Packed into Fewer Storage Slots

#### Issue
The order of storage variables can be reordered in a way to reduce the usage amount of storage slots.
```
Reference from solidity documentation:
Finally, in order to allow the EVM to optimize for this, ensure that you try to order your storage 
variables and struct members such that they can be packed tightly. For example, declaring your 
storage variables in the order of uint128, uint128, uint256 instead of uint128, uint256, uint128, 
as the former will only take up two slots of storage whereas the latter will take up three.
```
https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html#layout-of-state-variables-in-storage

#### PoC
Total of 1 instance found.

1. OlympusHeart Contract
We can save 1 storage slot by reordering it like below.
Move bool variable (1 byte size) to pack it with address variable (20 bytes size).

Before:
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L32-L48

Change to:
```solidity
    /// @notice Timestamp of the last beat (UTC, in seconds)
    uint256 public lastBeat;

    /// @notice Reward for beating the Heart (in reward token decimals)
    uint256 public reward;

    /// @notice Reward token address that users are sent for beating the Heart
    ERC20 public rewardToken;

    /// @notice Status of the Heart, false = stopped, true = beating
    bool public active;

    // Modules
    OlympusPrice internal PRICE;

    // Policies
    IOperator internal _operator;
```

#### Mitigation
Reorder storage variables like shown in above PoC.

&ensp;
### Unchanging State Variable Should be Immutable

#### Issue
State variable that is only set in the constructor and can't be changed afterwards,
should be declared as immutable.

#### PoC
Total of 2 instances found.

1. ohm variable of BondCallback.sol
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L32

2. aggregator variable of BondCallback.sol
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L28

#### Mitigation
Change to immutable.

&ensp;
### Change Function Visibility Public to External

#### Issue
If the function is not called internally, it is cheaper to set your function visibility to external instead of public.

#### PoC
Total of 4 instances found.

1. Governance.sol:getMetadata()
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L145

2. Governance.sol:getActiveProposal()
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L151

3. Kernel.sol:grantRole()
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L439

4. Kernel.sol:revokeRole()
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L451


#### Mitigation
Change the function visibility to external.

&ensp;
### Internal Function Called Only Once Can be Inlined

#### Issue
Certain function is defined even though it is called only once.
Inline it instead to where it is called to avoid usage of extra gas.

#### PoC
Total of 9 instances found.

1. _issueReward() of Heart.sol
this function called only once at line 106
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L111-L114
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L106

2. _addObservation() of Operator.sol
this function called only once at line 201
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L652-L695
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L201

3. _installModule() of Kernel.sol
this function called only once at line 239
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L266-L277
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L239

4. _upgradeModule() of Kernel.sol
this function called only once at line 243
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L279-L293
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L243

5. _activatePolicy() of Kernel.sol
this function called only once at line 246
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L295-L315
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L246

6. _deactivatePolicy() of Kernel.sol
this function called only once at line 249
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L325-L346
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L249

7. _migrateKernel() of Kernel.sol
this function called only once at line 256
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L351-L372
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L256

8. _reconfigurePolicies() of Kernel.sol
this function called only once at line 292
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L378-L389
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L292

9. _pruneFromDependents() of Kernel.sol
this function called only once at line 342
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L409-L432
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L342

#### Mitigation
I recommend to not define above functions and instead inline it at place it is called.

&ensp;
### Use Bit Shifting Instead of Multiplication/Division of 2

#### Issue
The MUL and DIV opcodes cost 5 gas but SHL and SHR only costs 3 gas.
Since MUL/DIV and SHL/SHR result the same, use cheaper bit shifting.

#### PoC
Total of 5 instances found.
```solidity
./Operator.sol:372:            int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
./Operator.sol:427:            int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
./Operator.sol:419:            uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
./Operator.sol:420:            uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
./Operator.sol:786:                ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
```

#### Mitigation
Use bit shifting instead of multiplication/division.
Example:
```solidity
uint256 center = upper - (upper - lower) / 2; 
Good:
uint256 center = upper - (upper - lower) >> 2; 
```

&ensp;
### Use Calldata instead of Memory for Read Only Function Parameters

#### Issue
It is cheaper gas to use calldata than memory if the function parameter is read only.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, 
and behaves mostly like memory. More details on following link.
link: https://docs.soliditylang.org/en/v0.8.15/types.html#data-location

#### PoC
Total of 4 instances found.
```
./Governance.sol:162:        string memory proposalURI_
./PRICE.sol:205:    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
./TreasuryCustodian.sol:53:    function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {
./BondCallback.sol:152:    function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {
```

#### Mitigation
Change memory to calldata

&ensp;
### Boolean Comparisons

#### Issue
It is more gas expensive to compare boolean with "variable == true" or "variable == false" than 
directly checking the returned boolean value.

#### PoC
Total of 2 instances found.

```
./Governance.sol:223:        if (proposalHasBeenActivated[proposalId_] == true) {
./Governance.sol:306:        if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {
```

#### Mitigation
Simply check by returned boolean value.
Change it to
```
if (proposalHasBeenActivated[proposalId_])
```

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size.

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 42 instances found.
```
./PriceConfig.sol:58:    function changeMovingAverageDuration(uint48 movingAverageDuration_)
./PriceConfig.sol:69:    function changeObservationFrequency(uint48 observationFrequency_)
./IOperator.sol:85:    function setCushionFactor(uint32 cushionFactor_) external;
./IOperator.sol:93:        uint32 duration_,
./IOperator.sol:94:        uint32 debtBuffer_,
./IOperator.sol:95:        uint32 depositInterval_
./IOperator.sol:101:    function setReserveFactor(uint32 reserveFactor_) external;
./IOperator.sol:110:        uint32 wait_,
./IOperator.sol:111:        uint32 threshold_,
./IOperator.sol:112:        uint32 observe_
./TRSRY.sol:51:    function VERSION() external pure override returns (uint8 major, uint8 minor) {
./VOTES.sol:27:    function VERSION() external pure override returns (uint8 major, uint8 minor) {
./MINTR.sol:25:    function VERSION() external pure override returns (uint8 major, uint8 minor) {
./PRICE.sol:27:    event MovingAverageDurationChanged(uint48 movingAverageDuration_);
./PRICE.sol:28:    event ObservationFrequencyChanged(uint48 observationFrequency_);
./PRICE.sol:75:        uint48 observationFrequency_,
./PRICE.sol:76:        uint48 movingAverageDuration_
./PRICE.sol:84:        uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();
./PRICE.sol:87:        uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();
./PRICE.sol:113:    function VERSION() external pure override returns (uint8 major, uint8 minor) {
./PRICE.sol:127:        uint32 numObs = numObservations;
./PRICE.sol:185:        uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;
./PRICE.sol:240:    function changeMovingAverageDuration(uint48 movingAverageDuration_) external permissioned {
./PRICE.sol:266:    function changeObservationFrequency(uint48 observationFrequency_) external permissioned {
./Operator.sol:51:    event CushionFactorChanged(uint32 cushionFactor_);
./Operator.sol:52:    event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);
./Operator.sol:53:    event ReserveFactorChanged(uint32 reserveFactor_);
./Operator.sol:54:    event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);
./Operator.sol:97:        uint32[8] memory configParams // [cushionFactor, cushionDuration, cushionDebtBuffer, cushionDepositInterval, reserveFactor, regenWait, regenThreshold, regenObserve]
./Operator.sol:418:            uint8 oracleDecimals = PRICE.decimals();
./Operator.sol:516:    function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {
./Operator.sol:528:        uint32 duration_,
./Operator.sol:529:        uint32 debtBuffer_,
./Operator.sol:530:        uint32 depositInterval_
./Operator.sol:548:    function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {
./Operator.sol:560:        uint32 wait_,
./Operator.sol:561:        uint32 threshold_,
./Operator.sol:562:        uint32 observe_
./Operator.sol:665:        uint32 observe = _config.regenObserve;
./INSTR.sol:28:    function VERSION() public pure override returns (uint8 major, uint8 minor) {
./Kernel.sol:100:    function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}
./RANGE.sol:115:    function VERSION() external pure override returns (uint8 major, uint8 minor) {
```

#### Mitigation
I suggest using uint256 instead of anything smaller or downcast where needed.

&ensp;
### Unnecessary Default Value Initialization

#### Issue
When variable is not initialized, it will have its default values.
For example, 0 for uint, false for bool and address(0) for address.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#scoping-and-declarations

#### PoC
Total of 3 instances found.
```
./KernelUtils.sol:43:    for (uint256 i = 0; i < 5; ) {
./KernelUtils.sol:58:    for (uint256 i = 0; i < 32; ) {
./Kernel.sol:397:        for (uint256 i = 0; i < reqLength; ) {
```

#### Mitigation
I suggest removing default value initialization.
For example,
- for (uint256 i; i < 5; ) {

&ensp;
### Store Array's Length as a Variable 

#### Issue
By storing an array's length as a variable before the for-loop,
can save 3 gas per iteration.

#### PoC
Total of 1 instance found.
```
./Governance.sol:278:        for (uint256 step; step < instructions.length; ) {
```

#### Mitigation
Store array's length as a variable before looping it.
For example, I suggest changing it to
```
uint256 length = instructions.length;
for (uint256 step; step < length; ) {
```

&ensp;
### ++i Costs Less Gas than i++

#### Issue
Prefix increments/decrements (++i or --i) costs cheaper gas than 
postfix increment/decrements (i++ or i--).

#### PoC
Total of 7 instances found.
```
./KernelUtils.sol:49:            i++;
./KernelUtils.sol:64:            i++;
./Operator.sol:488:            decimals++;
./Operator.sol:670:                _status.low.count++;
./Operator.sol:686:                _status.high.count++;
./Operator.sol:675:                _status.low.count--;
./Operator.sol:691:                _status.high.count--;
```

#### Mitigation
Change it to postfix increments/decrements.
It saves 6 gas per loop.

&ensp;