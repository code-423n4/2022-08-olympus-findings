# 2022-08-OLYMPUS
## Low Risk and Non-Critical Issues

### Events not emmited on important state changes
Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

_There are **13** instances of this issue:_

```solidity
File: src/Kernel.sol

139:  function configureDependencies() external virtual returns (Keycode[] memory dependencies) {}

143:  function requestPermissions() external view virtual returns (Permissions[] memory requests) {}
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol

```solidity
File: src/modules/PRICE.sol

205:  function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol

```solidity
File: src/policies/BondCallback.sol

190:  function setOperator(Operator operator_) external onlyRole("callback_admin") {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol

```solidity
File: src/policies/Heart.sol

69:   function configureDependencies() external override returns (Keycode[] memory dependencies) {

130:  function resetBeat() external onlyRole("heart_admin") {

135:  function toggleBeat() external onlyRole("heart_admin") {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol

```solidity
File: src/policies/Operator.sol

154:  function configureDependencies() external override returns (Keycode[] memory dependencies) {

586:  function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_)

598:  function initialize() external onlyRole("operator_admin") {

624:  function toggleActive() external onlyRole("operator_admin") {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

```solidity
File: src/policies/PriceConfig.sol

18:   function configureDependencies() external override returns (Keycode[] memory dependencies) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol

```solidity
File: src/policies/TreasuryCustodian.sol

27:   function configureDependencies() external override returns (Keycode[] memory dependencies) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol

### `public` functions not called by the contract should be declared `external` instead


_There are **18** instances of this issue:_

```solidity
File: src/Kernel.sol

439:  function grantRole(Role role_, address addr_) public onlyAdmin {

451:  function revokeRole(Role role_, address addr_) public onlyAdmin {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol

```solidity
File: src/modules/INSTR.sol

28:   function VERSION() public pure override returns (uint8 major, uint8 minor) {

37:   function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/INSTR.sol

```solidity
File: src/modules/MINTR.sol

20:   function KEYCODE() public pure override returns (Keycode) {

33:   function mintOhm(address to_, uint256 amount_) public permissioned {

37:   function burnOhm(address from_, uint256 amount_) public permissioned {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol

```solidity
File: src/modules/PRICE.sol

108:  function KEYCODE() public pure override returns (Keycode) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol

```solidity
File: src/modules/RANGE.sol

110:  function KEYCODE() public pure override returns (Keycode) {

215:  function updateMarket(
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol

```solidity
File: src/modules/TRSRY.sol

47:   function KEYCODE() public pure override returns (Keycode) {

75:   function withdrawReserves(
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol

```solidity
File: src/modules/VOTES.sol

22:   function KEYCODE() public pure override returns (Keycode) {

45:   function transfer(address to_, uint256 amount_) public pure override returns (bool) {

51:   function transferFrom(
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol

```solidity
File: src/policies/Governance.sol

145:  function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {

151:  function getActiveProposal() public view returns (ActivatedProposal memory) {
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol

```solidity
File: src/policies/Heart.sol

18:     /// @dev    The Olympus Heart contract provides keeper rewards to call the heart beat function which fuels
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol

### Left ToDos
Code architecture, incentives and error handling questions should be resolved before deployment

_There are **1** instances of this issue:_

```solidity
File: src/policies/Operator.sol

657:  /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

### Use `1e18` instead of `10**18` or `1000000000000000000`


_There are **13** instances of this issue:_

```solidity
File: src/modules/PRICE.sol

91:   _scaleFactor = 10**exponent;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol

```solidity
File: src/policies/Operator.sol

375:  uint256 oracleScale = 10**uint8(int8(PRICE.decimals()) - priceDecimals);

376:  uint256 bondScale = 10 **

419:  uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;

420:  uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;

430:  uint256 oracleScale = 10**uint8(int8(oracleDecimals) - priceDecimals);

431:  uint256 bondScale = 10 **

753:  10**reserveDecimals * RANGE.price(true, false),

754:  10**ohmDecimals * 10**PRICE.decimals()

764:  10**ohmDecimals * 10**PRICE.decimals(),

765:  10**reserveDecimals * RANGE.price(true, true)

784:  10**ohmDecimals * 10**PRICE.decimals(),

785:  10**reserveDecimals * RANGE.price(true, true)
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

### Non-library/interface files should use fixed compiler versions, not floating ones


_There are **1** instances of this issue:_

```solidity
File: src/interfaces/IBondCallback.sol

2:    pragma solidity >=0.8.0;
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/interfaces/IBondCallback.sol

### Natspec is missing


_There are **2** instances of this issue:_

```solidity
File: src/policies/TreasuryCustodian.sol

      /// @audit
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol

```solidity
File: src/utils/KernelUtils.sol

      /// @audit
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol

### Event is missing `indexed` fields


_There are **12** instances of this issue:_

```solidity
File: src/modules/PRICE.sol

event      NewObservation(uint256 timestamp_, uint256 price_, uint256 movingAverage_)
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol

```solidity
File: src/modules/RANGE.sol

event      WallUp(bool high_, uint256 timestamp_, uint256 capacity_)

event      WallDown(bool high_, uint256 timestamp_, uint256 capacity_)

event      CushionUp(bool high_, uint256 timestamp_, uint256 capacity_)
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol

```solidity
File: src/modules/TRSRY.sol

event      ApprovedForWithdrawal(address indexed policy_, ERC20 indexed token_, uint256 amount_)

event      DebtIncurred(ERC20 indexed token_, address indexed policy_, uint256 amount_)

event      DebtRepaid(ERC20 indexed token_, address indexed policy_, uint256 amount_)

event      DebtSet(ERC20 indexed token_, address indexed policy_, uint256 amount_)
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol

```solidity
File: src/policies/Governance.sol

event      ProposalEndorsed(uint256 proposalId, address voter, uint256 amount)

event      WalletVoted(uint256 proposalId, address voter, bool for_, uint256 userVotes)
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol

```solidity
File: src/policies/Operator.sol

event      CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_)

event      RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_)
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol

### Not used import


_There are **2** instances of this issue:_

```solidity
File: src/Kernel.sol

4:    import "src/utils/KernelUtils.sol";
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol

```solidity
File: src/utils/KernelUtils.sol

4:    import {Keycode, Role} from "../Kernel.sol";
```
https://github.com/code-423n4/2022-08-olympus/tree/main/src/utils/KernelUtils.sol
