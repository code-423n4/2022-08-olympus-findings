# 2022-08-OLYMPUS
## Gas Optimizations Report

### The usage of `++i` will cost less gas than `i++`. The same change can be applied to `i--` as well.
This change would save up to 6 gas per instance/loop.

_There are **7** instances of this issue:_

```solidity
File: src/policies/Operator.sol

488:  decimals++;

670:  _status.low.count++;

675:  _status.low.count--;

686:  _status.high.count++;

691:  _status.high.count--;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

```solidity
File: src/utils/KernelUtils.sol

49:   i++;

64:   i++;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol

### State variables should be cached in stack variables rather than re-reading them.
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

_There are **20** instances of this issue:_

```solidity
File: src/modules/RANGE.sol

      /// @audit Cache `_range`. Used 6 times in `updatePrices`
160:  uint256 wallSpread = _range.wall.spread;
161:  uint256 cushionSpread = _range.cushion.spread;
173:  _range.wall.low.price,
174:  _range.cushion.low.price,
175:  _range.cushion.high.price,
176:  _range.wall.high.price

      /// @audit Cache `_range`. Used 4 times in `price`
305:  return _range.wall.high.price;
307:  return _range.wall.low.price;
311:  return _range.cushion.high.price;
313:  return _range.cushion.low.price;

      /// @audit Cache `FACTOR_SCALE`. Used 5 times in `updatePrices`
164:  _range.wall.low.price = (movingAverage_ * (FACTOR_SCALE - wallSpread)) / FACTOR_SCALE;
165:  _range.wall.high.price = (movingAverage_ * (FACTOR_SCALE + wallSpread)) / FACTOR_SCALE;
167:  _range.cushion.low.price = (movingAverage_ * (FACTOR_SCALE - cushionSpread)) / FACTOR_SCALE;
169:  (movingAverage_ * (FACTOR_SCALE + cushionSpread)) /
170:  FACTOR_SCALE;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol

```solidity
File: src/policies/BondCallback.sol

      /// @audit Cache `ohm`. Used 3 times in `callback`
118:  if (quoteToken == payoutToken && quoteToken == ohm) {
125:  } else if (quoteToken == ohm) {
131:  } else if (payoutToken == ohm) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol

```solidity
File: src/policies/Operator.sol

      /// @audit Cache `_status`. Used 6 times in `_addObservation`
666:  Regen memory regen = _status.low;
670:  _status.low.count++;
675:  _status.low.count--;
682:  regen = _status.high;
686:  _status.high.count++;
691:  _status.high.count--;

      /// @audit Cache `RANGE`. Used 7 times in `requestPermissions`
172:  Keycode RANGE_KEYCODE = RANGE.KEYCODE();
177:  requests[0] = Permissions(RANGE_KEYCODE, RANGE.updateCapacity.selector);
178:  requests[1] = Permissions(RANGE_KEYCODE, RANGE.updateMarket.selector);
179:  requests[2] = Permissions(RANGE_KEYCODE, RANGE.updatePrices.selector);
180:  requests[3] = Permissions(RANGE_KEYCODE, RANGE.regenerate.selector);
181:  requests[4] = Permissions(RANGE_KEYCODE, RANGE.setSpreads.selector);
182:  requests[5] = Permissions(RANGE_KEYCODE, RANGE.setThresholdFactor.selector);

      /// @audit Cache `RANGE`. Used 3 times in `operate`
210:  uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&
216:  uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&
223:  OlympusRange.Range memory range = RANGE.range();

      /// @audit Cache `RANGE`. Used 3 times in `_activate`
364:  OlympusRange.Range memory range = RANGE.range();
415:  RANGE.updateMarket(true, market, marketCapacity);
467:  RANGE.updateMarket(false, market, marketCapacity);

      /// @audit Cache `RANGE`. Used 3 times in `_checkCushion`
735:  bool sideActive = RANGE.active(high_);
736:  uint256 market = RANGE.market(high_);
741:  RANGE.capacity(high_) < auctioneer.currentCapacity(market))

      /// @audit Cache `RANGE`. Used 4 times in `getAmountOut`
753:  10**reserveDecimals * RANGE.price(true, false),
758:  if (amountOut > RANGE.capacity(false)) revert Operator_InsufficientCapacity();
765:  10**reserveDecimals * RANGE.price(true, true)
769:  if (amountOut > RANGE.capacity(true)) revert Operator_InsufficientCapacity();

      /// @audit Cache `MINTR`. Used 3 times in `requestPermissions`
174:  Keycode MINTR_KEYCODE = MINTR.KEYCODE();
184:  requests[7] = Permissions(MINTR_KEYCODE, MINTR.mintOhm.selector);
185:  requests[8] = Permissions(MINTR_KEYCODE, MINTR.burnOhm.selector);

      /// @audit Cache `auctioneer`. Used 4 times in `_activate`
409:  uint256 market = auctioneer.createMarket(params);
412:  callback.whitelist(address(auctioneer.getTeller()), market);
461:  uint256 market = auctioneer.createMarket(params);
464:  callback.whitelist(address(auctioneer.getTeller()), market);

      /// @audit Cache `callback`. Used 4 times in `_activate`
397:  callbackAddr: address(callback),
412:  callback.whitelist(address(auctioneer.getTeller()), market);
449:  callbackAddr: address(callback),
464:  callback.whitelist(address(auctioneer.getTeller()), market);

      /// @audit Cache `ohm`. Used 4 times in `swap`
277:  if (tokenIn_ == ohm) {
299:  ohm.safeTransferFrom(msg.sender, address(this), amountIn_);
307:  emit Swap(ohm, reserve, amountIn_, amountOut);
335:  emit Swap(reserve, ohm, amountIn_, amountOut);

      /// @audit Cache `ohmDecimals`. Used 4 times in `_activate`
372:  int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
378:  36 + scaleAdjustment + int8(reserveDecimals) - int8(ohmDecimals) - priceDecimals
427:  int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
433:  36 + scaleAdjustment + int8(ohmDecimals) - int8(reserveDecimals) - priceDecimals

      /// @audit Cache `reserve`. Used 5 times in `swap`
305:  TRSRY.withdrawReserves(msg.sender, reserve, amountOut);
307:  emit Swap(ohm, reserve, amountIn_, amountOut);
308:  } else if (tokenIn_ == reserve) {
330:  reserve.safeTransferFrom(msg.sender, address(TRSRY), amountIn_);
335:  emit Swap(reserve, ohm, amountIn_, amountOut);

      /// @audit Cache `reserveDecimals`. Used 4 times in `_activate`
372:  int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
378:  36 + scaleAdjustment + int8(reserveDecimals) - int8(ohmDecimals) - priceDecimals
427:  int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
433:  36 + scaleAdjustment + int8(ohmDecimals) - int8(reserveDecimals) - priceDecimals

      /// @audit Cache `FACTOR_SCALE`. Used 3 times in `fullCapacity`
780:  uint256 capacity = (reservesInTreasury * _config.reserveFactor) / FACTOR_SCALE;
786:  ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
787:  FACTOR_SCALE;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

```solidity
File: src/policies/PriceConfig.sol

      /// @audit Cache `PRICE`. Used 3 times in `requestPermissions`
32:   permissions[0] = Permissions(PRICE.KEYCODE(), PRICE.initialize.selector);
33:   permissions[1] = Permissions(PRICE.KEYCODE(), PRICE.changeMovingAverageDuration.selector);
34:   permissions[2] = Permissions(PRICE.KEYCODE(), PRICE.changeObservationFrequency.selector);
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol

```solidity
File: src/policies/TreasuryCustodian.sol

      /// @audit Cache `TRSRY`. Used 3 times in `requestPermissions`
35:   Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();
38:   requests[0] = Permissions(TRSRY_KEYCODE, TRSRY.setApprovalFor.selector);
39:   requests[1] = Permissions(TRSRY_KEYCODE, TRSRY.setDebt.selector);
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol

### It costs more gas to initialize non-`constant`/non-`immutable` variables to zero than to let the default of zero be applied
Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

_There are **3** instances of this issue:_

```solidity
File: src/Kernel.sol

397:  for (uint256 i = 0; i < reqLength; ) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol

```solidity
File: src/utils/KernelUtils.sol

43:   for (uint256 i = 0; i < 5; ) {

58:   for (uint256 i = 0; i < 32; ) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol

### Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

_There are **9** instances of this issue:_

```solidity
File: src/modules/PRICE.sol

59:   uint8 public constant decimals = 18;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol

```solidity
File: src/modules/RANGE.sol

65:   uint256 public constant FACTOR_SCALE = 1e4;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol

```solidity
File: src/policies/Governance.sol

121:  uint256 public constant SUBMISSION_REQUIREMENT = 100;

124:  uint256 public constant ACTIVATION_DEADLINE = 2 weeks;

127:  uint256 public constant GRACE_PERIOD = 1 weeks;

130:  uint256 public constant ENDORSEMENT_THRESHOLD = 20;

133:  uint256 public constant EXECUTION_THRESHOLD = 33;

137:  uint256 public constant EXECUTION_TIMELOCK = 3 days;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol

```solidity
File: src/policies/Operator.sol

89:   uint32 public constant FACTOR_SCALE = 1e4;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

### Functions that are access-restricted from most users may be marked as `payable`
Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **2** instances of this issue:_

```solidity
File: src/Kernel.sol

439:  function grantRole(Role role_, address addr_) public onlyAdmin {

451:  function revokeRole(Role role_, address addr_) public onlyAdmin {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops
When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **2** instances of this issue:_

```solidity
File: src/policies/Governance.sol

281:  ++step;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol

```solidity
File: src/policies/TreasuryCustodian.sol

62:   ++j;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol

### `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables


_There are **11** instances of this issue:_

```solidity
File: src/modules/PRICE.sol

136:  _movingAverage += (currentPrice - earliestPrice) / numObs;

138:  _movingAverage -= (earliestPrice - currentPrice) / numObs;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol

```solidity
File: src/modules/TRSRY.sol

97:   totalDebt[token_] += amount_;

116:  totalDebt[token_] -= received;

131:  if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;

132:  else totalDebt[token_] -= oldDebt - amount_;

96:   reserveDebt[token_][msg.sender] += amount_;

115:  reserveDebt[token_][msg.sender] -= received;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol

```solidity
File: src/policies/BondCallback.sol

143:  _amountsPerMarket[id_][0] += inputAmount_;

144:  _amountsPerMarket[id_][1] += outputAmount_;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol

```solidity
File: src/policies/Heart.sol

103:  lastBeat += frequency();
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol

### Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead
'When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.' \ https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html \ Use a larger size then downcast where needed

_There are **54** instances of this issue:_

```solidity
File: src/Kernel.sol

100:  function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol

```solidity
File: src/modules/INSTR.sol

28:   function VERSION() public pure override returns (uint8 major, uint8 minor) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/INSTR.sol

```solidity
File: src/modules/MINTR.sol

25:   function VERSION() external pure override returns (uint8 major, uint8 minor) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol

```solidity
File: src/modules/PRICE.sol

44:   uint32 public nextObsIndex;

47:   uint32 public numObservations;

59:   uint8 public constant decimals = 18;

84:   uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();

87:   uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();

113:  function VERSION() external pure override returns (uint8 major, uint8 minor) {

127:  uint32 numObs = numObservations;

185:  uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol

```solidity
File: src/modules/RANGE.sol

115:  function VERSION() external pure override returns (uint8 major, uint8 minor) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol

```solidity
File: src/modules/TRSRY.sol

51:   function VERSION() external pure override returns (uint8 major, uint8 minor) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol

```solidity
File: src/modules/VOTES.sol

27:   function VERSION() external pure override returns (uint8 major, uint8 minor) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol

```solidity
File: src/policies/interfaces/IOperator.sol

13:   uint32 cushionFactor;

14:   uint32 cushionDuration;

15:   uint32 cushionDebtBuffer;

16:   uint32 cushionDepositInterval;

17:   uint32 reserveFactor;

18:   uint32 regenWait;

19:   uint32 regenThreshold;

20:   uint32 regenObserve;

31:   uint32 count;

33:   uint32 nextObservation;

85:   function setCushionFactor(uint32 cushionFactor_) external;

93:   uint32 duration_,

94:   uint32 debtBuffer_,

95:   uint32 depositInterval_

101:  function setReserveFactor(uint32 reserveFactor_) external;

110:  uint32 wait_,

111:  uint32 threshold_,

112:  uint32 observe_
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol

```solidity
File: src/policies/Operator.sol

51:   event CushionFactorChanged(uint32 cushionFactor_);

52:   event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);

53:   event ReserveFactorChanged(uint32 reserveFactor_);

54:   event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);

83:   uint8 public immutable ohmDecimals;

86:   uint8 public immutable reserveDecimals;

89:   uint32 public constant FACTOR_SCALE = 1e4;

371:  int8 priceDecimals = _getPriceDecimals(range.cushion.high.price);

372:  int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);

418:  uint8 oracleDecimals = PRICE.decimals();

426:  int8 priceDecimals = _getPriceDecimals(invCushionPrice);

427:  int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);

485:  int8 decimals;

516:  function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {

528:  uint32 duration_,

529:  uint32 debtBuffer_,

530:  uint32 depositInterval_

548:  function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {

560:  uint32 wait_,

561:  uint32 threshold_,

562:  uint32 observe_

665:  uint32 observe = _config.regenObserve;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

### Don't compare boolean expressions to boolean literals
Use `if(x)`/`if(!x)` instead of `if(x == true)`/`if(x == false)`.

_There are **2** instances of this issue:_

```solidity
File: src/policies/Governance.sol

223:  if (proposalHasBeenActivated[proposalId_] == true) {

306:  if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol

### Use `calldata` instead of `memory` for function parameters
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

_There are **6** instances of this issue:_

```solidity
File: src/Kernel.sol

393:  Permissions[] memory requests_,
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol

```solidity
File: src/modules/PRICE.sol

205:  function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol

```solidity
File: src/policies/BondCallback.sol

152:  function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol

```solidity
File: src/policies/Governance.sol

162:  string memory proposalURI_
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol

```solidity
File: src/policies/PriceConfig.sol

45:   function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol

```solidity
File: src/policies/TreasuryCustodian.sol

53:   function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`
In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **7** instances of this issue:_

```solidity
File: src/policies/Operator.sol

210:  uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&

211:  _status.high.count >= config_.regenThreshold

216:  uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&

217:  _status.low.count >= config_.regenThreshold

486:  while (price_ >= 10) {

667:  if (currentPrice >= movingAverage) {

683:  if (currentPrice <= movingAverage) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

### Use `immutable` & `constant` for state variables that do not change their value


_There are **9** instances of this issue:_

```solidity
File: src/modules/RANGE.sol

59:   Range internal _range;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol

```solidity
File: src/policies/BondCallback.sol

28:   IBondAggregator public aggregator;

32:   ERC20 public ohm;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol

```solidity
File: src/policies/Heart.sol

33:   bool public active;

48:   IOperator internal _operator;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol

```solidity
File: src/policies/Operator.sol

59:   Status internal _status;

60:   Config internal _config;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

```solidity
File: src/policies/PriceConfig.sol

11:   OlympusPrice internal PRICE;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol

```solidity
File: src/policies/TreasuryCustodian.sol

20:   OlympusTreasury internal TRSRY;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol

### Using bools for storage variables incurs overhead
Use `uint256(1)` and `uint256(2)` for `true`/`false` to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

_There are **4** instances of this issue:_

```solidity
File: src/modules/PRICE.sol

62:   bool public initialized;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol

```solidity
File: src/policies/Heart.sol

33:   bool public active;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol

```solidity
File: src/policies/Operator.sol

63:   bool public initialized;

66:   bool public active;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

### Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

_There are **3** instances of this issue:_

```solidity
File: src/interfaces/IBondCallback.sol

2:    pragma solidity >=0.8.0;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/interfaces/IBondCallback.sol

```solidity
File: src/policies/interfaces/IHeart.sol

2:    pragma solidity >=0.8.0;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IHeart.sol

```solidity
File: src/policies/interfaces/IOperator.sol

2:    pragma solidity >=0.8.0;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol
