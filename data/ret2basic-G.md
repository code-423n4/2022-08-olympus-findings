# Olympus DAO Contest Gas Optimization Report

## Summary

These gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (66 instances)
2. Cache `<array>.length` (2 instances)
3. Use `unchecked{}` to suppress overflow/underflow check (43 instances)
4. Long `require()`/`revert()` string (8 instances)
5. Using `bool`s for storage incurs overhead (20 instances)
6. Use `!= 0` instead of `> 0` when comparing uint (11 instances)
7. Empty blocks should be removed (42 instances)
8. Don't initialize variables with default value (5 instances)
9. Use `++i`/`--i` instead of `i++`/`i--` (6 instances)
10. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (3 instances)
11. Use uint256/int256 instead of other variations (245 instances)
12. Use `abi.encodePacked()` instead of `abi.encode()` (41 instances)
13. Use `private` instead of `public` for constants (17 instances)
14. Don't compare boolean expressions to boolean literals (4 instances)
15. Use custom errors instead of `revert()`/`require()` strings (24 instances)
16. Use shift right/left instead of division/multiplication if possible (9 instances)

Total 546 instances of 16 issues.

## 1. Use `calldata` instead of `memory` (66 instances)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
2022-08-olympus/src/Kernel.sol::139 => function configureDependencies() external virtual returns (Keycode[] memory dependencies) {}

2022-08-olympus/src/Kernel.sol::143 => function requestPermissions() external view virtual returns (Permissions[] memory requests) {}

2022-08-olympus/src/external/OlympusERC20.sol::133 => function tryRecover(bytes32 hash, bytes memory signature)

2022-08-olympus/src/external/OlympusERC20.sol::182 => function recover(bytes32 hash, bytes memory signature) internal pure returns (address) {

2022-08-olympus/src/external/OlympusERC20.sol::689 => function name() public view returns (string memory) {

2022-08-olympus/src/external/OlympusERC20.sol::693 => function symbol() public view returns (string memory) {

2022-08-olympus/src/interfaces/AggregatorV2V3Interface.sol::23 => function description() external view returns (string memory);

2022-08-olympus/src/interfaces/IBondAggregator.sol::82 => function marketsFor(address payout_, address quote_) external view returns (uint256[] memory);

2022-08-olympus/src/interfaces/IBondAuctioneer.sol::62 => function createMarket(MarketParams memory params_) external returns (uint256);

2022-08-olympus/src/interfaces/IBondAuctioneer.sol::116 => function setDefaults(uint32[6] memory defaults_) external;

2022-08-olympus/src/interfaces/IBondTeller.sol::43 => function claimFees(ERC20[] memory tokens_, address to_) external;

2022-08-olympus/src/modules/INSTR.sol::37 => function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {

2022-08-olympus/src/modules/PRICE.sol::205 => function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)

2022-08-olympus/src/modules/RANGE.sol::275 => function range() external view returns (Range memory) {

2022-08-olympus/src/policies/BondCallback.sol::48 => function configureDependencies() external override returns (Keycode[] memory dependencies) {

2022-08-olympus/src/policies/BondCallback.sol::152 => function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {

2022-08-olympus/src/policies/Governance.sol::61 => function configureDependencies() external override returns (Keycode[] memory dependencies) {

2022-08-olympus/src/policies/Governance.sol::145 => function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {

2022-08-olympus/src/policies/Governance.sol::151 => function getActiveProposal() public view returns (ActivatedProposal memory) {

2022-08-olympus/src/policies/Heart.sol::69 => function configureDependencies() external override returns (Keycode[] memory dependencies) {

2022-08-olympus/src/policies/Operator.sol::154 => function configureDependencies() external override returns (Keycode[] memory dependencies) {

2022-08-olympus/src/policies/Operator.sol::171 => function requestPermissions() external view override returns (Permissions[] memory requests) {

2022-08-olympus/src/policies/Operator.sol::793 => function status() external view override returns (Status memory) {

2022-08-olympus/src/policies/Operator.sol::798 => function config() external view override returns (Config memory) {

2022-08-olympus/src/policies/PriceConfig.sol::18 => function configureDependencies() external override returns (Keycode[] memory dependencies) {

2022-08-olympus/src/policies/PriceConfig.sol::45 => function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)

2022-08-olympus/src/policies/TreasuryCustodian.sol::27 => function configureDependencies() external override returns (Keycode[] memory dependencies) {

2022-08-olympus/src/policies/TreasuryCustodian.sol::34 => function requestPermissions() external view override returns (Permissions[] memory requests) {

2022-08-olympus/src/policies/TreasuryCustodian.sol::53 => function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {

2022-08-olympus/src/policies/VoterRegistration.sol::19 => function configureDependencies() external override returns (Keycode[] memory dependencies) {

2022-08-olympus/src/policies/interfaces/IOperator.sol::146 => function status() external view returns (Status memory);

2022-08-olympus/src/policies/interfaces/IOperator.sol::149 => function config() external view returns (Config memory);

2022-08-olympus/src/test/lib/ModuleTestFixtureGenerator.sol::35 => function requestPermissions() external view override returns (Permissions[] memory requests) {

2022-08-olympus/src/test/lib/ModuleTestFixtureGenerator.sol::47 => function generateFixture(Module module_, Permissions[] memory requests_)

2022-08-olympus/src/test/lib/ModuleTestFixtureGenerator.sol::55 => function generateGodmodeFixture(Module module_, string memory contractName_)

2022-08-olympus/src/test/lib/UserFactory.sol::23 => function create(uint256 userNum) public returns (address[] memory) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::176 => function marketsFor(address payout_, address quote_) public view returns (uint256[] memory) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::242 => function liveMarketsBy(address owner_) external view returns (uint256[] memory) {

2022-08-olympus/src/test/lib/bonds/BondFixedTermCDA.sol::33 => function createMarket(MarketParams memory params_) external override returns (uint256) {

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::129 => function createMarket(MarketParams memory params_) external virtual returns (uint256);

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::132 => function _createMarket(MarketParams memory params_) internal returns (uint256) {

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::338 => function setDefaults(uint32[6] memory defaults_) external override requiresAuth {

2022-08-olympus/src/test/lib/bonds/bases/BondBaseTeller.sol::100 => function claimFees(ERC20[] memory tokens_, address to_) external override {

2022-08-olympus/src/test/lib/bonds/bases/BondBaseTeller.sol::285 => function _uint2str(uint256 _i) internal pure returns (string memory) {

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAggregator.sol::82 => function marketsFor(address payout_, address quote_) external view returns (uint256[] memory);

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAuctioneer.sol::62 => function createMarket(MarketParams memory params_) external returns (uint256);

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAuctioneer.sol::116 => function setDefaults(uint32[6] memory defaults_) external;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondFixedTermTeller.sol::43 => function batchRedeem(uint256[] memory tokenIds_, uint256[] memory amounts_) external;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondTeller.sol::43 => function claimFees(ERC20[] memory tokens_, address to_) external;

2022-08-olympus/src/test/lib/bonds/lib/ERC1155.sol::120 => function balanceOfBatch(address[] memory owners, uint256[] memory ids)

2022-08-olympus/src/test/lib/larping.sol::89 => function larp(function () external returns(string memory) f, string memory returned1) internal {

2022-08-olympus/src/test/lib/larping.sol::97 => function larpp(function () external payable returns(string memory) f, string memory returned1) internal {

2022-08-olympus/src/test/lib/larping.sol::105 => function larpv(function () external view returns(string memory) f, string memory returned1) internal {

2022-08-olympus/src/test/lib/quabi/Quabi.sol::10 => function jq(string memory query, string memory path)

2022-08-olympus/src/test/lib/quabi/Quabi.sol::23 => function getPath(string memory contractName) internal returns (string memory path) {

2022-08-olympus/src/test/lib/quabi/Quabi.sol::33 => function getSelectors(string memory query, string memory path) internal returns (bytes4[] memory) {

2022-08-olympus/src/test/lib/quabi/Quabi.sol::47 => function getFunctions(string memory contractName) public returns (bytes4[] memory) {

2022-08-olympus/src/test/lib/quabi/Quabi.sol::54 => function getFunctionsWithModifier(string memory contractName, string memory modifierName) public returns (bytes4[] memory) {

2022-08-olympus/src/test/mocks/KernelTestMocks.sol::11 => function configureDependencies() external override returns (Keycode[] memory dependencies) {

2022-08-olympus/src/test/mocks/KernelTestMocks.sol::18 => function requestPermissions() external view override returns (Permissions[] memory requests) {

2022-08-olympus/src/test/mocks/MockModuleWriter.sol::22 => function configureDependencies() external override returns (Keycode[] memory dependencies) {}

2022-08-olympus/src/test/mocks/MockPrice.sol::44 => function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::58 => function description() external view override returns (string memory) {}

2022-08-olympus/src/test/policies/Heart.t.sol::31 => function configureDependencies() external override returns (Keycode[] memory dependencies) {}

2022-08-olympus/src/test/policies/Heart.t.sol::33 => function requestPermissions() external view override returns (Permissions[] memory requests) {}

2022-08-olympus/src/test/policies/PriceConfig.t.sol::102 => function getObs(uint8 nonce) internal returns (uint256[] memory) {
```

## 2. Cache `<array>.length` (2 instances)

If `<array>.length` is used as for loop termination condition, then the `.length` method will be called in each iteration. Caching it in a local variable can save gas.

```solidity
2022-08-olympus/src/policies/Governance.sol::278 => for (uint256 step; step < instructions.length; ) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::182 => uint256 len = forPayout.length;
```

## 3. Use `unchecked{}` to suppress overflow/underflow check (43 instances)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
2022-08-olympus/src/scripts/Deploy.sol::239 => for (uint i = 0; i < 90; i++) {

2022-08-olympus/src/test/lib/ModuleTestFixtureGenerator.sol::19 => for (uint256 i; i < len; i++) {

2022-08-olympus/src/test/lib/ModuleTestFixtureGenerator.sol::38 => for (uint256 i; i < len; i++) {

2022-08-olympus/src/test/lib/ModuleTestFixtureGenerator.sol::66 => for (uint256 i; i < num; ++i) {

2022-08-olympus/src/test/lib/UserFactory.sol::25 => for (uint256 i = 0; i < userNum; i++) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::129 => for (uint256 i = firstIndex_; i < lastIndex_; ++i) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::135 => for (uint256 i = firstIndex_; i < lastIndex_; ++i) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::158 => for (uint256 i; i < len; ++i) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::165 => for (uint256 i; i < len; ++i) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::183 => for (uint256 i; i < len; ++i) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::186 => if (isLive(forPayout[i]) && address(quoteToken) == quote_) ++count;

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::192 => for (uint256 i; i < len; ++i) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::221 => for (uint256 i; i < len; ++i) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::245 => for (uint256 i; i < marketCounter; ++i) {

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::254 => for (uint256 i; i < marketCounter; ++i) {

2022-08-olympus/src/test/lib/bonds/BondFixedTermTeller.sol::161 => for (uint256 i; i < len; ++i) {

2022-08-olympus/src/test/lib/bonds/bases/BondBaseTeller.sol::102 => for (uint256 i; i < len; ++i) {

2022-08-olympus/src/test/lib/bonds/lib/ERC1155.sol::135 => for (uint256 i; i < ownersLength; ++i) {

2022-08-olympus/src/test/mocks/MockModuleWriter.sol::16 => for (uint256 i; i < len; i++) {

2022-08-olympus/src/test/mocks/MockModuleWriter.sol::32 => for (uint256 i; i < len; i++) {

2022-08-olympus/src/test/modules/PRICE.t.sol::99 => for (uint256 i; i < numObservations; ++i) {

2022-08-olympus/src/test/modules/PRICE.t.sol::126 => for (uint256 i; i < observations; ++i) {

2022-08-olympus/src/test/modules/PRICE.t.sol::224 => for (uint256 i; i < numObs; ++i) {

2022-08-olympus/src/test/modules/PRICE.t.sol::313 => for (uint256 i; i < price.numObservations(); ++i) {

2022-08-olympus/src/test/modules/PRICE.t.sol::386 => for (uint256 i; i < numObservations; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::776 => for (uint256 i; i < 4; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::948 => for (uint256 i; i < 5; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::992 => for (uint256 i; i < 8; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1004 => for (uint256 i; i < 5; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1045 => for (uint256 i; i < 4; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1097 => for (uint256 i; i < 3; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1106 => for (uint256 i; i < 4; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1149 => for (uint256 i; i < 5; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1217 => for (uint256 i; i < 5; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1261 => for (uint256 i; i < 8; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1273 => for (uint256 i; i < 5; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1314 => for (uint256 i; i < 4; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1366 => for (uint256 i; i < 3; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1375 => for (uint256 i; i < 4; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1418 => for (uint256 i; i < 5; ++i) {

2022-08-olympus/src/test/policies/Operator.t.sol::1832 => for (uint256 i; i < 15; ++i) {

2022-08-olympus/src/test/policies/PriceConfig.t.sol::122 => for (uint256 i; i < numObservations; ++i) {

2022-08-olympus/src/test/policies/PriceConfig.t.sol::172 => for (uint256 i; i < numObservations; ++i) {
```

## 4. Long `require()`/`revert()` string (8 instances)

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

```solidity
2022-08-olympus/src/external/OlympusERC20.sol::107 => revert("ECDSA: invalid signature 's' value");

2022-08-olympus/src/external/OlympusERC20.sol::109 => revert("ECDSA: invalid signature 'v' value");

2022-08-olympus/src/external/OlympusERC20.sol::597 => require(c / a == b, "SafeMath: multiplication overflow");

2022-08-olympus/src/external/OlympusERC20.sol::769 => require(sender != address(0), "ERC20: transfer from the zero address");

2022-08-olympus/src/external/OlympusERC20.sol::770 => require(recipient != address(0), "ERC20: transfer to the zero address");

2022-08-olympus/src/external/OlympusERC20.sol::788 => require(account != address(0), "ERC20: burn from the zero address");

2022-08-olympus/src/external/OlympusERC20.sol::802 => require(owner != address(0), "ERC20: approve from the zero address");

2022-08-olympus/src/external/OlympusERC20.sol::803 => require(spender != address(0), "ERC20: approve to the zero address");
```

## 5. Using `bool`s for storage incurs overhead (20 instances)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
2022-08-olympus/src/Kernel.sol::113 => bool public isActive;

2022-08-olympus/src/Kernel.sol::207 => bool granted_

2022-08-olympus/src/Kernel.sol::394 => bool grant_

2022-08-olympus/src/interfaces/IBondAuctioneer.sol::48 => bool capacityInQuote;

2022-08-olympus/src/modules/PRICE.sol::62 => bool public initialized;

2022-08-olympus/src/modules/RANGE.sol::44 => bool active; // Whether or not the side is active (i.e. the Operator is performing market operations on this side, true = active, false = inactive)

2022-08-olympus/src/modules/RANGE.sol::216 => bool high_,

2022-08-olympus/src/policies/Heart.sol::33 => bool public active;

2022-08-olympus/src/policies/Operator.sol::63 => bool public initialized;

2022-08-olympus/src/policies/Operator.sol::66 => bool public active;

2022-08-olympus/src/policies/Operator.sol::735 => bool sideActive = RANGE.active(high_);

2022-08-olympus/src/policies/interfaces/IOperator.sol::34 => bool[] observations; // individual observations: true = price on other side of average, false = price on same side of average

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::83 => bool public allowNewMarkets;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::636 => bool active

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAuctioneer.sol::48 => bool capacityInQuote;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondCDA.sol::14 => bool capacityInQuote; // capacity limit is in payment token (true) or in payout (false, default)

2022-08-olympus/src/test/lib/bonds/interfaces/IBondCDA.sol::52 => bool active;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondFixedTermTeller.sol::9 => bool active;

2022-08-olympus/src/test/mocks/MockPrice.sol::15 => bool public result;

2022-08-olympus/src/test/policies/Heart.t.sol::23 => bool public result;
```

## 6. Use `!= 0` instead of `> 0` when comparing uint (11 instances)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
2022-08-olympus/src/external/OlympusERC20.sol::245 => if (uint256(s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) {

2022-08-olympus/src/external/OlympusERC20.sol::611 => require(b > 0, errorMessage);

2022-08-olympus/src/libraries/FullMath.sol::35 => require(denominator > 0);

2022-08-olympus/src/libraries/FullMath.sol::122 => if (mulmod(a, b, denominator) > 0) {

2022-08-olympus/src/policies/Governance.sol::247 => if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {

2022-08-olympus/src/test/modules/TRSRY.t.sol::98 => vm.assume(amount_ > 0);

2022-08-olympus/src/test/modules/TRSRY.t.sol::108 => vm.assume(amount_ > 0);

2022-08-olympus/src/test/modules/TRSRY.t.sol::126 => vm.assume(amount_ > 0);

2022-08-olympus/src/test/modules/TRSRY.t.sol::143 => vm.assume(amount_ > 0);

2022-08-olympus/src/utils/KernelUtils.sol::46 => if (char < 0x41 || char > 0x5A) revert InvalidKeycode(keycode_); // A-Z only

2022-08-olympus/src/utils/KernelUtils.sol::60 => if ((char < 0x61 || char > 0x7A) && char != 0x5f && char != 0x00) {
```

## 7. Empty blocks should be removed (42 instances)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
2022-08-olympus/src/Kernel.sol::85 => constructor(Kernel kernel_) KernelAdapter(kernel_) {}

2022-08-olympus/src/Kernel.sol::95 => function KEYCODE() public pure virtual returns (Keycode) {}

2022-08-olympus/src/Kernel.sol::100 => function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}

2022-08-olympus/src/Kernel.sol::105 => function INIT() external virtual onlyKernel {}

2022-08-olympus/src/Kernel.sol::115 => constructor(Kernel kernel_) KernelAdapter(kernel_) {}

2022-08-olympus/src/Kernel.sol::139 => function configureDependencies() external virtual returns (Keycode[] memory dependencies) {}

2022-08-olympus/src/Kernel.sol::143 => function requestPermissions() external view virtual returns (Permissions[] memory requests) {}

2022-08-olympus/src/external/OlympusERC20.sol::813 => ) internal virtual {}

2022-08-olympus/src/external/OlympusERC20.sol::844 => constructor(string memory name) EIP712(name, "1") {}

2022-08-olympus/src/external/OlympusERC20.sol::908 => {}

2022-08-olympus/src/interfaces/AggregatorV2V3Interface.sol::53 => interface AggregatorV2V3Interface is AggregatorInterface, AggregatorV3Interface {}

2022-08-olympus/src/modules/INSTR.sol::20 => constructor(Kernel kernel_) Module(kernel_) {}

2022-08-olympus/src/modules/TRSRY.sol::45 => constructor(Kernel kernel_) Module(kernel_) {}

2022-08-olympus/src/modules/VOTES.sol::19 => {}

2022-08-olympus/src/policies/Governance.sol::59 => constructor(Kernel kernel_) Policy(kernel_) {}

2022-08-olympus/src/policies/PriceConfig.sol::15 => constructor(Kernel kernel_) Policy(kernel_) {}

2022-08-olympus/src/policies/TreasuryCustodian.sol::24 => constructor(Kernel kernel_) Policy(kernel_) {}

2022-08-olympus/src/policies/VoterRegistration.sol::16 => constructor(Kernel kernel_) Policy(kernel_) {}

2022-08-olympus/src/test/lib/bonds/BondAggregator.sol::57 => constructor(address guardian_, Authority authority_) Auth(guardian_, authority_) {}

2022-08-olympus/src/test/lib/bonds/BondFixedTermCDA.sol::29 => ) BondBaseCDA(teller_, aggregator_, guardian_, authority_) {}

2022-08-olympus/src/test/lib/bonds/BondFixedTermTeller.sol::50 => ) BondBaseTeller(protocol_, aggregator_, guardian_, authority_) {}

2022-08-olympus/src/test/mocks/Faucet.sol::76 => receive() external payable {}

2022-08-olympus/src/test/mocks/KernelTestMocks.sol::9 => constructor(Kernel kernel_) Policy(kernel_) {}

2022-08-olympus/src/test/mocks/KernelTestMocks.sol::33 => constructor(Kernel kernel_) Module(kernel_) {}

2022-08-olympus/src/test/mocks/KernelTestMocks.sol::78 => constructor(Kernel kernel_) Module(kernel_) {}

2022-08-olympus/src/test/mocks/KernelTestMocks.sol::87 => function INIT() public override onlyKernel {}

2022-08-olympus/src/test/mocks/MockInvalidModule.sol::7 => constructor(Kernel kernel_) Module(kernel_) {}

2022-08-olympus/src/test/mocks/MockModuleWriter.sol::22 => function configureDependencies() external override returns (Keycode[] memory dependencies) {}

2022-08-olympus/src/test/mocks/MockPrice.sol::46 => {}

2022-08-olympus/src/test/mocks/MockPrice.sol::48 => function changeMovingAverageDuration(uint48 movingAverageDuration_) external {}

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::52 => function latestRound() external view override returns (uint256) {}

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::54 => function getAnswer(uint256 roundId) external view override returns (int256) {}

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::56 => function getTimestamp(uint256 roundId) external view override returns (uint256) {}

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::58 => function description() external view override returns (string memory) {}

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::60 => function version() external view override returns (uint256) {}

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::76 => {}

2022-08-olympus/src/test/mocks/MockValidModule.sol::9 => constructor(Kernel kernel_) Module(kernel_) {}

2022-08-olympus/src/test/mocks/MockValidUpgradedModule.sol::9 => constructor(Kernel kernel_) Module(kernel_) {}

2022-08-olympus/src/test/policies/BondCallback.t.sol::35 => ) ERC20(_name, _symbol, _decimals) {}

2022-08-olympus/src/test/policies/Heart.t.sol::31 => function configureDependencies() external override returns (Keycode[] memory dependencies) {}

2022-08-olympus/src/test/policies/Heart.t.sol::33 => function requestPermissions() external view override returns (Permissions[] memory requests) {}

2022-08-olympus/src/test/policies/Operator.t.sol::34 => ) ERC20(_name, _symbol, _decimals) {}
```

## 8. Don't initialize variables with default value (5 instances)

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```solidity
2022-08-olympus/src/Kernel.sol::397 => for (uint256 i = 0; i < reqLength; ) {

2022-08-olympus/src/scripts/Deploy.sol::239 => for (uint i = 0; i < 90; i++) {

2022-08-olympus/src/test/lib/UserFactory.sol::25 => for (uint256 i = 0; i < userNum; i++) {

2022-08-olympus/src/utils/KernelUtils.sol::43 => for (uint256 i = 0; i < 5; ) {

2022-08-olympus/src/utils/KernelUtils.sol::58 => for (uint256 i = 0; i < 32; ) {
```

## 9. Use `++i`/`--i` instead of `i++`/`i--` (6 instances)

Using `++i`/`--i` saves 6 gas per loop.

```solidity
2022-08-olympus/src/scripts/Deploy.sol::239 => for (uint i = 0; i < 90; i++) {

2022-08-olympus/src/test/lib/ModuleTestFixtureGenerator.sol::19 => for (uint256 i; i < len; i++) {

2022-08-olympus/src/test/lib/ModuleTestFixtureGenerator.sol::38 => for (uint256 i; i < len; i++) {

2022-08-olympus/src/test/lib/UserFactory.sol::25 => for (uint256 i = 0; i < userNum; i++) {

2022-08-olympus/src/test/mocks/MockModuleWriter.sol::16 => for (uint256 i; i < len; i++) {

2022-08-olympus/src/test/mocks/MockModuleWriter.sol::32 => for (uint256 i; i < len; i++) {
```

## 10. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (3 instances)

Instead of using operator && on single require check, using double `require()` checks can save more gas.

```solidity
2022-08-olympus/src/libraries/TransferHelper.sol::20 => require(success && (data.length == 0 || abi.decode(data, (bool))), "TRANSFER_FROM_FAILED");

2022-08-olympus/src/libraries/TransferHelper.sol::32 => require(success && (data.length == 0 || abi.decode(data, (bool))), "TRANSFER_FAILED");

2022-08-olympus/src/libraries/TransferHelper.sol::44 => require(success && (data.length == 0 || abi.decode(data, (bool))), "APPROVE_FAILED");
```

## 11. Use uint256/int256 instead of other variations (245 instances)

Using smaller data types such as uint8/int8 is more expensive than using uint256/int256. The EVM works with 256bit/32byte words. Every operation is based on these base units. If the data is smaller, further operations are needed to downscale from 256 bits to 8 bits, and this is more expensive than using uint256/int256.

```solidity
2022-08-olympus/src/Kernel.sol::100 => function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}

2022-08-olympus/src/external/OlympusERC20.sol::144 => uint8 v;

2022-08-olympus/src/external/OlympusERC20.sol::201 => uint8 v;

2022-08-olympus/src/external/OlympusERC20.sol::232 => uint8 v,

2022-08-olympus/src/external/OlympusERC20.sol::267 => uint8 v,

2022-08-olympus/src/external/OlympusERC20.sol::458 => uint8 v,

2022-08-olympus/src/external/OlympusERC20.sol::677 => uint8 internal immutable _decimals;

2022-08-olympus/src/external/OlympusERC20.sol::682 => uint8 decimals_

2022-08-olympus/src/external/OlympusERC20.sol::697 => function decimals() public view virtual returns (uint8) {

2022-08-olympus/src/external/OlympusERC20.sol::854 => uint8 v,

2022-08-olympus/src/interfaces/AggregatorV2V3Interface.sol::21 => function decimals() external view returns (uint8);

2022-08-olympus/src/interfaces/AggregatorV2V3Interface.sol::30 => function getRoundData(uint80 _roundId)

2022-08-olympus/src/interfaces/AggregatorV2V3Interface.sol::34 => uint80 roundId,

2022-08-olympus/src/interfaces/AggregatorV2V3Interface.sol::38 => uint80 answeredInRound

2022-08-olympus/src/interfaces/AggregatorV2V3Interface.sol::45 => uint80 roundId,

2022-08-olympus/src/interfaces/AggregatorV2V3Interface.sol::49 => uint80 answeredInRound

2022-08-olympus/src/interfaces/IBondAuctioneer.sol::52 => uint32 debtBuffer;

2022-08-olympus/src/interfaces/IBondAuctioneer.sol::55 => uint32 depositInterval;

2022-08-olympus/src/interfaces/IBondAuctioneer.sol::56 => int8 scaleAdjustment;

2022-08-olympus/src/interfaces/IBondAuctioneer.sol::90 => function setIntervals(uint256 id_, uint32[3] calldata intervals_) external;

2022-08-olympus/src/interfaces/IBondAuctioneer.sol::116 => function setDefaults(uint32[6] memory defaults_) external;

2022-08-olympus/src/modules/INSTR.sol::28 => function VERSION() public pure override returns (uint8 major, uint8 minor) {

2022-08-olympus/src/modules/MINTR.sol::25 => function VERSION() external pure override returns (uint8 major, uint8 minor) {

2022-08-olympus/src/modules/PRICE.sol::44 => uint32 public nextObsIndex;

2022-08-olympus/src/modules/PRICE.sol::47 => uint32 public numObservations;

2022-08-olympus/src/modules/PRICE.sol::59 => uint8 public constant decimals = 18;

2022-08-olympus/src/modules/PRICE.sol::84 => uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();

2022-08-olympus/src/modules/PRICE.sol::87 => uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();

2022-08-olympus/src/modules/PRICE.sol::97 => numObservations = uint32(movingAverageDuration_ / observationFrequency_);

2022-08-olympus/src/modules/PRICE.sol::113 => function VERSION() external pure override returns (uint8 major, uint8 minor) {

2022-08-olympus/src/modules/PRICE.sol::127 => uint32 numObs = numObservations;

2022-08-olympus/src/modules/PRICE.sol::185 => uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;

2022-08-olympus/src/modules/PRICE.sol::257 => numObservations = uint32(newObservations);

2022-08-olympus/src/modules/PRICE.sol::289 => numObservations = uint32(newObservations);

2022-08-olympus/src/modules/RANGE.sol::115 => function VERSION() external pure override returns (uint8 major, uint8 minor) {

2022-08-olympus/src/modules/TRSRY.sol::51 => function VERSION() external pure override returns (uint8 major, uint8 minor) {

2022-08-olympus/src/modules/VOTES.sol::27 => function VERSION() external pure override returns (uint8 major, uint8 minor) {

2022-08-olympus/src/policies/Operator.sol::51 => event CushionFactorChanged(uint32 cushionFactor_);

2022-08-olympus/src/policies/Operator.sol::52 => event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);

2022-08-olympus/src/policies/Operator.sol::53 => event ReserveFactorChanged(uint32 reserveFactor_);

2022-08-olympus/src/policies/Operator.sol::54 => event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);

2022-08-olympus/src/policies/Operator.sol::83 => uint8 public immutable ohmDecimals;

2022-08-olympus/src/policies/Operator.sol::86 => uint8 public immutable reserveDecimals;

2022-08-olympus/src/policies/Operator.sol::89 => uint32 public constant FACTOR_SCALE = 1e4;

2022-08-olympus/src/policies/Operator.sol::97 => uint32[8] memory configParams // [cushionFactor, cushionDuration, cushionDebtBuffer, cushionDepositInterval, reserveFactor, regenWait, regenThreshold, regenObserve]

2022-08-olympus/src/policies/Operator.sol::106 => if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();

2022-08-olympus/src/policies/Operator.sol::108 => if (configParams[3] < uint32(1 hours) || configParams[3] > configParams[1])

2022-08-olympus/src/policies/Operator.sol::116 => configParams[7] == uint32(0)

2022-08-olympus/src/policies/Operator.sol::127 => count: uint32(0),

2022-08-olympus/src/policies/Operator.sol::129 => nextObservation: uint32(0),

2022-08-olympus/src/policies/Operator.sol::371 => int8 priceDecimals = _getPriceDecimals(range.cushion.high.price);

2022-08-olympus/src/policies/Operator.sol::372 => int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);

2022-08-olympus/src/policies/Operator.sol::375 => uint256 oracleScale = 10**uint8(int8(PRICE.decimals()) - priceDecimals);

2022-08-olympus/src/policies/Operator.sol::377 => uint8(

2022-08-olympus/src/policies/Operator.sol::378 => 36 + scaleAdjustment + int8(reserveDecimals) - int8(ohmDecimals) - priceDecimals

2022-08-olympus/src/policies/Operator.sol::418 => uint8 oracleDecimals = PRICE.decimals();

2022-08-olympus/src/policies/Operator.sol::426 => int8 priceDecimals = _getPriceDecimals(invCushionPrice);

2022-08-olympus/src/policies/Operator.sol::427 => int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);

2022-08-olympus/src/policies/Operator.sol::430 => uint256 oracleScale = 10**uint8(int8(oracleDecimals) - priceDecimals);

2022-08-olympus/src/policies/Operator.sol::432 => uint8(

2022-08-olympus/src/policies/Operator.sol::433 => 36 + scaleAdjustment + int8(ohmDecimals) - int8(reserveDecimals) - priceDecimals

2022-08-olympus/src/policies/Operator.sol::484 => function _getPriceDecimals(uint256 price_) internal view returns (int8) {

2022-08-olympus/src/policies/Operator.sol::485 => int8 decimals;

2022-08-olympus/src/policies/Operator.sol::493 => return decimals - int8(PRICE.decimals());

2022-08-olympus/src/policies/Operator.sol::516 => function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {

2022-08-olympus/src/policies/Operator.sol::528 => uint32 duration_,

2022-08-olympus/src/policies/Operator.sol::529 => uint32 debtBuffer_,

2022-08-olympus/src/policies/Operator.sol::530 => uint32 depositInterval_

2022-08-olympus/src/policies/Operator.sol::535 => if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();

2022-08-olympus/src/policies/Operator.sol::536 => if (depositInterval_ < uint32(1 hours) || depositInterval_ > duration_)

2022-08-olympus/src/policies/Operator.sol::548 => function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {

2022-08-olympus/src/policies/Operator.sol::560 => uint32 wait_,

2022-08-olympus/src/policies/Operator.sol::561 => uint32 threshold_,

2022-08-olympus/src/policies/Operator.sol::562 => uint32 observe_

2022-08-olympus/src/policies/Operator.sol::665 => uint32 observe = _config.regenObserve;

2022-08-olympus/src/policies/Operator.sol::705 => _status.high.count = uint32(0);

2022-08-olympus/src/policies/Operator.sol::707 => _status.high.nextObservation = uint32(0);

2022-08-olympus/src/policies/Operator.sol::717 => _status.low.count = uint32(0);

2022-08-olympus/src/policies/Operator.sol::719 => _status.low.nextObservation = uint32(0);

2022-08-olympus/src/policies/interfaces/IOperator.sol::13 => uint32 cushionFactor; // percent of capacity to be used for a single cushion deployment, assumes 2 decimals (i.e. 1000 = 10%)

2022-08-olympus/src/policies/interfaces/IOperator.sol::14 => uint32 cushionDuration; // duration of a single cushion deployment in seconds

2022-08-olympus/src/policies/interfaces/IOperator.sol::15 => uint32 cushionDebtBuffer; // Percentage over the initial debt to allow the market to accumulate at any one time. Percent with 3 decimals, e.g. 1_000 = 1 %. See IBondAuctioneer for more info.

2022-08-olympus/src/policies/interfaces/IOperator.sol::16 => uint32 cushionDepositInterval; // Target frequency of deposits. Determines max payout of the bond market. See IBondAuctioneer for more info.

2022-08-olympus/src/policies/interfaces/IOperator.sol::17 => uint32 reserveFactor; // percent of reserves in treasury to be used for a single wall, assumes 2 decimals (i.e. 1000 = 10%)

2022-08-olympus/src/policies/interfaces/IOperator.sol::18 => uint32 regenWait; // minimum duration to wait to reinstate a wall in seconds

2022-08-olympus/src/policies/interfaces/IOperator.sol::19 => uint32 regenThreshold; // number of price points on other side of moving average to reinstate a wall

2022-08-olympus/src/policies/interfaces/IOperator.sol::20 => uint32 regenObserve; // number of price points to observe to determine regeneration

2022-08-olympus/src/policies/interfaces/IOperator.sol::31 => uint32 count; // current number of price points that count towards regeneration

2022-08-olympus/src/policies/interfaces/IOperator.sol::33 => uint32 nextObservation; // index of the next observation in the observations array

2022-08-olympus/src/policies/interfaces/IOperator.sol::85 => function setCushionFactor(uint32 cushionFactor_) external;

2022-08-olympus/src/policies/interfaces/IOperator.sol::93 => uint32 duration_,

2022-08-olympus/src/policies/interfaces/IOperator.sol::94 => uint32 debtBuffer_,

2022-08-olympus/src/policies/interfaces/IOperator.sol::95 => uint32 depositInterval_

2022-08-olympus/src/policies/interfaces/IOperator.sol::101 => function setReserveFactor(uint32 reserveFactor_) external;

2022-08-olympus/src/policies/interfaces/IOperator.sol::110 => uint32 wait_,

2022-08-olympus/src/policies/interfaces/IOperator.sol::111 => uint32 threshold_,

2022-08-olympus/src/policies/interfaces/IOperator.sol::112 => uint32 observe_

2022-08-olympus/src/scripts/Deploy.sol::137 => uint32(3000), // cushionFactor

2022-08-olympus/src/scripts/Deploy.sol::138 => uint32(3 days), // cushionDuration

2022-08-olympus/src/scripts/Deploy.sol::139 => uint32(100_000), // cushionDebtBuffer

2022-08-olympus/src/scripts/Deploy.sol::140 => uint32(1 hours), // cushionDepositInterval

2022-08-olympus/src/scripts/Deploy.sol::141 => uint32(800), // reserveFactor

2022-08-olympus/src/scripts/Deploy.sol::142 => uint32(1 hours), // regenWait

2022-08-olympus/src/scripts/Deploy.sol::143 => uint32(5), // regenThreshold // 18

2022-08-olympus/src/scripts/Deploy.sol::144 => uint32(7) // regenObserve    // 21

2022-08-olympus/src/test/lib/UserFactory.sol::9 => address(bytes20(uint160(uint256(keccak256("hevm cheat code")))));

2022-08-olympus/src/test/lib/UserFactory.sol::17 => address payable user = payable(address(uint160(uint256(nextUser))));

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::89 => uint32 public defaultTuneInterval;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::90 => uint32 public defaultTuneAdjustment;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::92 => uint32 public minDebtDecayInterval;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::93 => uint32 public minDepositInterval;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::94 => uint32 public minMarketDuration;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::95 => uint32 public minDebtBuffer;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::138 => uint8 payoutTokenDecimals = params_.payoutToken.decimals();

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::139 => uint8 quoteTokenDecimals = params_.quoteToken.decimals();

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::160 => scale = 10**uint8(36 + params_.scaleAdjustment);

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::169 => uint32 secondsToConclusion;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::170 => uint32 debtDecayInterval;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::172 => secondsToConclusion = uint32(params_.conclusion - block.timestamp);

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::182 => uint32 userDebtDecay = params_.depositInterval * 5;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::296 => function setIntervals(uint256 id_, uint32[3] calldata intervals_) external override {

2022-08-olympus/src/test/lib/bonds/bases/BondBaseCDA.sol::338 => function setDefaults(uint32[6] memory defaults_) external override requiresAuth {

2022-08-olympus/src/test/lib/bonds/bases/BondBaseTeller.sol::299 => uint8 temp = (48 + uint8(_i - (_i / 10) * 10));

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAuctioneer.sol::52 => uint32 debtBuffer;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAuctioneer.sol::55 => uint32 depositInterval;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAuctioneer.sol::56 => int8 scaleAdjustment;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAuctioneer.sol::90 => function setIntervals(uint256 id_, uint32[3] calldata intervals_) external;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondAuctioneer.sol::116 => function setDefaults(uint32[6] memory defaults_) external;

2022-08-olympus/src/test/lib/bonds/interfaces/IBondCDA.sol::37 => uint32 length; // time from creation to conclusion.

2022-08-olympus/src/test/lib/bonds/interfaces/IBondCDA.sol::38 => uint32 depositInterval; // target frequency of deposits

2022-08-olympus/src/test/lib/bonds/interfaces/IBondCDA.sol::39 => uint32 tuneInterval; // frequency of tuning

2022-08-olympus/src/test/lib/bonds/interfaces/IBondCDA.sol::40 => uint32 tuneAdjustmentDelay; // time to implement downward tuning adjustments

2022-08-olympus/src/test/lib/bonds/interfaces/IBondCDA.sol::41 => uint32 debtDecayInterval; // interval over which debt should decay completely

2022-08-olympus/src/test/lib/larping.sol::9 => address(bytes20(uint160(uint256(keccak256("hevm cheat code")))));

2022-08-olympus/src/test/lib/larping.sol::139 => function larp(function () external returns(uint8) f, uint8 returned1) internal {

2022-08-olympus/src/test/lib/larping.sol::147 => function larpp(function () external payable returns(uint8) f, uint8 returned1) internal {

2022-08-olympus/src/test/lib/larping.sol::155 => function larpv(function () external view returns(uint8) f, uint8 returned1) internal {

2022-08-olympus/src/test/lib/quabi/Quabi.sol::8 => Vm internal constant vm = Vm(address(bytes20(uint160(uint256(keccak256("hevm cheat code"))))));

2022-08-olympus/src/test/mocks/MockPrice.sol::14 => uint8 public decimals;

2022-08-olympus/src/test/mocks/MockPrice.sol::30 => function VERSION() external pure override returns (uint8 major, uint8 minor) {

2022-08-olympus/src/test/mocks/MockPrice.sol::80 => function setDecimals(uint8 decimals_) external {

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::8 => uint8 public s_decimals;

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::19 => function setDecimals(uint8 decimals_) public {

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::23 => function decimals() external view override returns (uint8) {

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::40 => uint80 roundId,

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::44 => uint80 answeredInRound

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::65 => function getRoundData(uint80 _roundId)

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::70 => uint80 roundId,

2022-08-olympus/src/test/mocks/MockPriceFeed.sol::74 => uint80 answeredInRound

2022-08-olympus/src/test/mocks/MockValidModule.sol::15 => function VERSION() external pure override returns (uint8 major, uint8 minor) {

2022-08-olympus/src/test/mocks/MockValidUpgradedModule.sol::15 => function VERSION() external pure override returns (uint8 major, uint8 minor) {

2022-08-olympus/src/test/modules/PRICE.t.sol::57 => uint48(8 hours), // uint32 observationFrequency_,

2022-08-olympus/src/test/modules/PRICE.t.sol::58 => uint48(7 days) // uint32 movingAverageDuration_,

2022-08-olympus/src/test/modules/PRICE.t.sol::79 => function initializePrice(uint8 nonce) internal {

2022-08-olympus/src/test/modules/PRICE.t.sol::118 => function makeRandomObservations(uint8 nonce, uint256 observations)

2022-08-olympus/src/test/modules/PRICE.t.sol::163 => function testCorrectness_onlyPermittedPoliciesCanCallUpdateMovingAverage(uint8 nonce) public {

2022-08-olympus/src/test/modules/PRICE.t.sol::177 => function testCorrectness_updateMovingAverage(uint8 nonce) public {

2022-08-olympus/src/test/modules/PRICE.t.sol::209 => function testCorrectness_updateMovingAverageMultipleTimes(uint8 nonce) public {

2022-08-olympus/src/test/modules/PRICE.t.sol::218 => assertEq(price.nextObsIndex(), uint32(15));

2022-08-olympus/src/test/modules/PRICE.t.sol::248 => function testCorrectness_getCurrentPrice(uint8 nonce) public {

2022-08-olympus/src/test/modules/PRICE.t.sol::290 => function testCorrectness_getLastPrice(uint8 nonce) public {

2022-08-olympus/src/test/modules/PRICE.t.sol::298 => uint32 numObservations = price.numObservations();

2022-08-olympus/src/test/modules/PRICE.t.sol::299 => uint32 nextObsIndex = price.nextObsIndex();

2022-08-olympus/src/test/modules/PRICE.t.sol::300 => uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;

2022-08-olympus/src/test/modules/PRICE.t.sol::304 => function testCorrectness_getMovingAverage(uint8 nonce) public {

2022-08-olympus/src/test/modules/PRICE.t.sol::348 => function testCorrectness_initialize(uint8 nonce) public {

2022-08-olympus/src/test/modules/PRICE.t.sol::369 => function testFail_cannotReinitialize(uint8 nonce) public {

2022-08-olympus/src/test/modules/PRICE.t.sol::416 => function testCorrectness_changeMovingAverageDuration(uint8 nonce) public {

2022-08-olympus/src/test/modules/PRICE.t.sol::451 => function testCorrectness_changeObservationFrequency(uint8 nonce) public {

2022-08-olympus/src/test/policies/BondCallback.t.sol::34 => uint8 _decimals

2022-08-olympus/src/test/policies/BondCallback.t.sol::140 => uint32(2000), // cushionFactor

2022-08-olympus/src/test/policies/BondCallback.t.sol::141 => uint32(5 days), // duration

2022-08-olympus/src/test/policies/BondCallback.t.sol::142 => uint32(100_000), // debtBuffer

2022-08-olympus/src/test/policies/BondCallback.t.sol::143 => uint32(1 hours), // depositInterval

2022-08-olympus/src/test/policies/BondCallback.t.sol::144 => uint32(1000), // reserveFactor

2022-08-olympus/src/test/policies/BondCallback.t.sol::145 => uint32(1 hours), // regenWait

2022-08-olympus/src/test/policies/BondCallback.t.sol::146 => uint32(5), // regenThreshold

2022-08-olympus/src/test/policies/BondCallback.t.sol::147 => uint32(7) // regenObserve

2022-08-olympus/src/test/policies/BondCallback.t.sol::246 => int8 _quotePriceDecimals,

2022-08-olympus/src/test/policies/BondCallback.t.sol::247 => int8 _payoutPriceDecimals,

2022-08-olympus/src/test/policies/BondCallback.t.sol::250 => uint8 _payoutDecimals = payoutToken.decimals();

2022-08-olympus/src/test/policies/BondCallback.t.sol::251 => uint8 _quoteDecimals = quoteToken.decimals();

2022-08-olympus/src/test/policies/BondCallback.t.sol::253 => uint256 capacity = 100_000 * 10**uint8(int8(_payoutDecimals) - _payoutPriceDecimals);

2022-08-olympus/src/test/policies/BondCallback.t.sol::255 => int8 scaleAdjustment = int8(_payoutDecimals) -

2022-08-olympus/src/test/policies/BondCallback.t.sol::256 => int8(_quoteDecimals) -

2022-08-olympus/src/test/policies/BondCallback.t.sol::263 => uint8(

2022-08-olympus/src/test/policies/BondCallback.t.sol::264 => int8(36 + _quoteDecimals - _payoutDecimals) +

2022-08-olympus/src/test/policies/BondCallback.t.sol::274 => uint8(

2022-08-olympus/src/test/policies/BondCallback.t.sol::275 => int8(36 + _quoteDecimals - _payoutDecimals) +

2022-08-olympus/src/test/policies/BondCallback.t.sol::290 => uint32(50_000), // uint32 debtBuffer

2022-08-olympus/src/test/policies/BondCallback.t.sol::293 => uint32(24 hours), // uint32 depositInterval (duration)

2022-08-olympus/src/test/policies/BondCallback.t.sol::294 => scaleAdjustment // int8 scaleAdjustment

2022-08-olympus/src/test/policies/Operator.t.sol::33 => uint8 _decimals

2022-08-olympus/src/test/policies/Operator.t.sol::132 => uint32(2000), // cushionFactor

2022-08-olympus/src/test/policies/Operator.t.sol::133 => uint32(5 days), // duration

2022-08-olympus/src/test/policies/Operator.t.sol::134 => uint32(100_000), // debtBuffer

2022-08-olympus/src/test/policies/Operator.t.sol::135 => uint32(1 hours), // depositInterval

2022-08-olympus/src/test/policies/Operator.t.sol::136 => uint32(1000), // reserveFactor

2022-08-olympus/src/test/policies/Operator.t.sol::137 => uint32(1 hours), // regenWait

2022-08-olympus/src/test/policies/Operator.t.sol::138 => uint32(5), // regenThreshold

2022-08-olympus/src/test/policies/Operator.t.sol::139 => uint32(7) // regenObserve

2022-08-olympus/src/test/policies/Operator.t.sol::1519 => operator.setCushionParams(uint32(6 hours), uint32(50_000), uint32(4 hours));

2022-08-olympus/src/test/policies/Operator.t.sol::1529 => operator.setRegenParams(uint32(1 days), uint32(8), uint32(11));

2022-08-olympus/src/test/policies/Operator.t.sol::1671 => operator.setCushionFactor(uint32(1000));

2022-08-olympus/src/test/policies/Operator.t.sol::1677 => assertEq(newConfig.cushionFactor, uint32(1000));

2022-08-olympus/src/test/policies/Operator.t.sol::1690 => operator.setCushionFactor(uint32(99));

2022-08-olympus/src/test/policies/Operator.t.sol::1695 => operator.setCushionFactor(uint32(10001));

2022-08-olympus/src/test/policies/Operator.t.sol::1708 => operator.setCushionParams(uint32(24 hours), uint32(50_000), uint32(4 hours));

2022-08-olympus/src/test/policies/Operator.t.sol::1714 => assertEq(newConfig.cushionDuration, uint32(24 hours));

2022-08-olympus/src/test/policies/Operator.t.sol::1716 => assertEq(newConfig.cushionDebtBuffer, uint32(50_000));

2022-08-olympus/src/test/policies/Operator.t.sol::1718 => assertEq(newConfig.cushionDepositInterval, uint32(4 hours));

2022-08-olympus/src/test/policies/Operator.t.sol::1731 => operator.setCushionParams(uint32(1 days) - 1, uint32(100_000), uint32(1 hours));

2022-08-olympus/src/test/policies/Operator.t.sol::1736 => operator.setCushionParams(uint32(7 days) + 1, uint32(100_000), uint32(1 hours));

2022-08-olympus/src/test/policies/Operator.t.sol::1741 => operator.setCushionParams(uint32(1 days), uint32(100_000), uint32(2 days));

2022-08-olympus/src/test/policies/Operator.t.sol::1746 => operator.setCushionParams(uint32(2 days), uint32(99), uint32(2 hours));

2022-08-olympus/src/test/policies/Operator.t.sol::1759 => operator.setReserveFactor(uint32(500));

2022-08-olympus/src/test/policies/Operator.t.sol::1765 => assertEq(newConfig.reserveFactor, uint32(500));

2022-08-olympus/src/test/policies/Operator.t.sol::1778 => operator.setReserveFactor(uint32(99));

2022-08-olympus/src/test/policies/Operator.t.sol::1783 => operator.setReserveFactor(uint32(10001));

2022-08-olympus/src/test/policies/Operator.t.sol::1799 => operator.setRegenParams(uint32(1 hours) - 1, uint32(11), uint32(15));

2022-08-olympus/src/test/policies/Operator.t.sol::1804 => operator.setRegenParams(uint32(1 days), uint32(0), uint32(0));

2022-08-olympus/src/test/policies/Operator.t.sol::1809 => operator.setRegenParams(uint32(1 days), uint32(10), uint32(9));

2022-08-olympus/src/test/policies/Operator.t.sol::1813 => operator.setRegenParams(uint32(1 days), uint32(11), uint32(15));

2022-08-olympus/src/test/policies/Operator.t.sol::1921 => assertEq(status.high.count, uint32(0));

2022-08-olympus/src/test/policies/Operator.t.sol::1922 => assertEq(status.high.nextObservation, uint32(0));

2022-08-olympus/src/test/policies/Operator.t.sol::1924 => assertEq(status.low.count, uint32(0));

2022-08-olympus/src/test/policies/Operator.t.sol::1925 => assertEq(status.low.nextObservation, uint32(0));

2022-08-olympus/src/test/policies/Operator.t.sol::1939 => assertEq(status.high.count, uint32(1));

2022-08-olympus/src/test/policies/Operator.t.sol::1940 => assertEq(status.high.nextObservation, uint32(2));

2022-08-olympus/src/test/policies/Operator.t.sol::1942 => assertEq(status.low.count, uint32(1));

2022-08-olympus/src/test/policies/Operator.t.sol::1943 => assertEq(status.low.nextObservation, uint32(2));

2022-08-olympus/src/test/policies/Operator.t.sol::1970 => assertEq(status.high.count, uint32(1));

2022-08-olympus/src/test/policies/Operator.t.sol::1971 => assertEq(status.high.nextObservation, uint32(2));

2022-08-olympus/src/test/policies/Operator.t.sol::1972 => assertEq(status.low.count, uint32(1));

2022-08-olympus/src/test/policies/Operator.t.sol::1973 => assertEq(status.low.nextObservation, uint32(2));

2022-08-olympus/src/test/policies/Operator.t.sol::1986 => assertEq(status.high.count, uint32(0));

2022-08-olympus/src/test/policies/Operator.t.sol::1987 => assertEq(status.high.nextObservation, uint32(0));

2022-08-olympus/src/test/policies/Operator.t.sol::1989 => assertEq(status.low.count, uint32(0));

2022-08-olympus/src/test/policies/Operator.t.sol::1990 => assertEq(status.low.nextObservation, uint32(0));

2022-08-olympus/src/test/policies/PriceConfig.t.sol::69 => uint48(8 hours), // uint32 observationFrequency_,

2022-08-olympus/src/test/policies/PriceConfig.t.sol::70 => uint48(7 days) // uint32 movingAverageDuration_,

2022-08-olympus/src/test/policies/PriceConfig.t.sol::102 => function getObs(uint8 nonce) internal returns (uint256[] memory) {

2022-08-olympus/src/test/policies/PriceConfig.t.sol::147 => function testCorrectness_initialize(uint8 nonce) public {

2022-08-olympus/src/test/policies/PriceConfig.t.sol::177 => function testCorrectness_changeMovingAverageDuration(uint8 nonce) public {

2022-08-olympus/src/test/policies/PriceConfig.t.sol::201 => function testCorrectness_changeObservationFrequency(uint8 nonce) public {
```

## 12. Use `abi.encodePacked()` instead of `abi.encode()` (41 instances)

`abi.encodePacked()` is more efficient than `abi.encode()`.

```solidity
2022-08-olympus/src/external/OlympusERC20.sol::398 => return keccak256(abi.encode(typeHash, nameHash, versionHash, chainID, address(this)));

2022-08-olympus/src/external/OlympusERC20.sol::861 => abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline)

2022-08-olympus/src/test/lib/larping.sol::18 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::26 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::34 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::43 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::51 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::59 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::68 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::76 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::84 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::93 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::101 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::109 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::118 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::126 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::134 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::143 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::151 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::159 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::168 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::176 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::184 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::193 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::201 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::209 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::218 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::226 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::234 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::243 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::251 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::259 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::268 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::276 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::284 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::293 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::301 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::309 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::318 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::326 => abi.encode(returned1)

2022-08-olympus/src/test/lib/larping.sol::334 => abi.encode(returned1)
```

## 13. Use `private` instead of `public` for constants (17 instances)

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

```solidity
2022-08-olympus/src/modules/PRICE.sol::59 => uint8 public constant decimals = 18;

2022-08-olympus/src/modules/RANGE.sol::65 => uint256 public constant FACTOR_SCALE = 1e4;

2022-08-olympus/src/policies/Governance.sol::121 => uint256 public constant SUBMISSION_REQUIREMENT = 100;

2022-08-olympus/src/policies/Governance.sol::124 => uint256 public constant ACTIVATION_DEADLINE = 2 weeks;

2022-08-olympus/src/policies/Governance.sol::127 => uint256 public constant GRACE_PERIOD = 1 weeks;

2022-08-olympus/src/policies/Governance.sol::130 => uint256 public constant ENDORSEMENT_THRESHOLD = 20;

2022-08-olympus/src/policies/Governance.sol::133 => uint256 public constant EXECUTION_THRESHOLD = 33;

2022-08-olympus/src/policies/Governance.sol::137 => uint256 public constant EXECUTION_TIMELOCK = 3 days;

2022-08-olympus/src/policies/Operator.sol::89 => uint32 public constant FACTOR_SCALE = 1e4;

2022-08-olympus/src/scripts/Deploy.sol::75 => ERC20 public constant ohm = ERC20(0x0595328847AF962F951a4f8F8eE9A3Bf261e4f6b); // OHM goerli address

2022-08-olympus/src/scripts/Deploy.sol::76 => ERC20 public constant reserve = ERC20(0x41e38e70a36150D08A8c97aEC194321b5eB545A5); // DAI goerli address

2022-08-olympus/src/scripts/Deploy.sol::77 => ERC20 public constant rewardToken = ERC20(0x0Bb7509324cE409F7bbC4b701f932eAca9736AB7); // WETH goerli address

2022-08-olympus/src/scripts/Deploy.sol::80 => IBondAuctioneer public constant bondAuctioneer =

2022-08-olympus/src/scripts/Deploy.sol::82 => IBondAggregator public constant bondAggregator =

2022-08-olympus/src/scripts/Deploy.sol::86 => AggregatorV2V3Interface public constant ohmEthPriceFeed =

2022-08-olympus/src/scripts/Deploy.sol::88 => AggregatorV2V3Interface public constant reserveEthPriceFeed =

2022-08-olympus/src/test/lib/bonds/bases/BondBaseTeller.sol::65 => uint48 public constant FEE_DECIMALS = 1e5; // one percent equals 1000.
```

## 14. Don't compare boolean expressions to boolean literals (4 instances)

`if (<x> == true)` can be refactored to `if (<x>)`, `if (<x> == false)` can be refactored to `if (!<x>)`.

```solidity
2022-08-olympus/src/policies/Governance.sol::223 => if (proposalHasBeenActivated[proposalId_] == true) {

2022-08-olympus/src/policies/Governance.sol::306 => if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {

2022-08-olympus/src/policies/Operator.sol::351 => if (id_ == RANGE.market(true)) {

2022-08-olympus/src/policies/Operator.sol::355 => if (id_ == RANGE.market(false)) {
```

## 15. Use custom errors instead of `revert()`/`require()` strings (24 instances)

Using `require()`/`revert()` strings is expensive. Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors.

Custom errors decrease both deploy and runtime gas costs. Note that runtime gas cost is only relevant when the revert condition is met.

```solidity
2022-08-olympus/src/external/OlympusERC20.sol::103 => revert("ECDSA: invalid signature");

2022-08-olympus/src/external/OlympusERC20.sol::105 => revert("ECDSA: invalid signature length");

2022-08-olympus/src/external/OlympusERC20.sol::107 => revert("ECDSA: invalid signature 's' value");

2022-08-olympus/src/external/OlympusERC20.sol::109 => revert("ECDSA: invalid signature 'v' value");

2022-08-olympus/src/external/OlympusERC20.sol::571 => require(c >= a, "SafeMath: addition overflow");

2022-08-olympus/src/external/OlympusERC20.sol::597 => require(c / a == b, "SafeMath: multiplication overflow");

2022-08-olympus/src/external/OlympusERC20.sol::769 => require(sender != address(0), "ERC20: transfer from the zero address");

2022-08-olympus/src/external/OlympusERC20.sol::770 => require(recipient != address(0), "ERC20: transfer to the zero address");

2022-08-olympus/src/external/OlympusERC20.sol::780 => require(account != address(0), "ERC20: mint to the zero address");

2022-08-olympus/src/external/OlympusERC20.sol::788 => require(account != address(0), "ERC20: burn from the zero address");

2022-08-olympus/src/external/OlympusERC20.sol::802 => require(owner != address(0), "ERC20: approve from the zero address");

2022-08-olympus/src/external/OlympusERC20.sol::803 => require(spender != address(0), "ERC20: approve to the zero address");

2022-08-olympus/src/external/OlympusERC20.sol::858 => require(block.timestamp <= deadline, "ERC20Permit: expired deadline");

2022-08-olympus/src/external/OlympusERC20.sol::867 => require(signer == owner, "ERC20Permit: invalid signature");

2022-08-olympus/src/libraries/TransferHelper.sol::20 => require(success && (data.length == 0 || abi.decode(data, (bool))), "TRANSFER_FROM_FAILED");

2022-08-olympus/src/libraries/TransferHelper.sol::32 => require(success && (data.length == 0 || abi.decode(data, (bool))), "TRANSFER_FAILED");

2022-08-olympus/src/libraries/TransferHelper.sol::44 => require(success && (data.length == 0 || abi.decode(data, (bool))), "APPROVE_FAILED");

2022-08-olympus/src/test/lib/bonds/lib/ERC1155.sol::57 => require(msg.sender == from || isApprovedForAll[from][msg.sender], "NOT_AUTHORIZED");

2022-08-olympus/src/test/lib/bonds/lib/ERC1155.sol::82 => require(idsLength == amounts.length, "LENGTH_MISMATCH");

2022-08-olympus/src/test/lib/bonds/lib/ERC1155.sol::84 => require(msg.sender == from || isApprovedForAll[from][msg.sender], "NOT_AUTHORIZED");

2022-08-olympus/src/test/lib/bonds/lib/ERC1155.sol::128 => require(ownersLength == ids.length, "LENGTH_MISMATCH");

2022-08-olympus/src/test/lib/bonds/lib/ERC1155.sol::188 => require(idsLength == amounts.length, "LENGTH_MISMATCH");

2022-08-olympus/src/test/lib/bonds/lib/ERC1155.sol::223 => require(idsLength == amounts.length, "LENGTH_MISMATCH");

2022-08-olympus/src/test/mocks/Faucet.sol::83 => require(success, "Withdraw Failed");
```

## 16. Use shift right/left instead of division/multiplication if possible (9 instances)

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left. While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```solidity
2022-08-olympus/src/policies/Operator.sol::372 => int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);

2022-08-olympus/src/policies/Operator.sol::419 => uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;

2022-08-olympus/src/policies/Operator.sol::420 => uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;

2022-08-olympus/src/policies/Operator.sol::427 => int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);

2022-08-olympus/src/test/lib/bonds/bases/BondBaseTeller.sol::253 => num1 = num1 - (146097 * num2 + 3) / 4;

2022-08-olympus/src/test/lib/bonds/bases/BondBaseTeller.sol::255 => num1 = num1 - (1461 * _year) / 4 + 31;

2022-08-olympus/src/test/policies/BondCallback.t.sol::271 => uint256 minimumPrice = (priceSignificand / 2) *

2022-08-olympus/src/test/policies/Operator.t.sol::869 => uint256 amountIn = auctioneer.maxAmountAccepted(id, guardian) / 2;

2022-08-olympus/src/test/policies/Operator.t.sol::900 => uint256 amountIn = auctioneer.maxAmountAccepted(id, guardian) / 2;
```
