## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-08-olympus/src/Kernel.sol::397 => for (uint256 i = 0; i < reqLength; ) {
2022-08-olympus/src/utils/KernelUtils.sol::43 => for (uint256 i = 0; i < 5; ) {
2022-08-olympus/src/utils/KernelUtils.sol::58 => for (uint256 i = 0; i < 32; ) {
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2022-08-olympus/src/policies/Governance.sol::278 => for (uint256 step; step < instructions.length; ) {
```

## [G-03] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2022-08-olympus/src/policies/Operator.sol::372 => int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
2022-08-olympus/src/policies/Operator.sol::419 => uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
2022-08-olympus/src/policies/Operator.sol::420 => uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
2022-08-olympus/src/policies/Operator.sol::427 => int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
```

## [G-04] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2022-08-olympus/src/modules/MINTR.sol::9 => OHM public immutable ohm;
2022-08-olympus/src/modules/RANGE.sol::68 => ERC20 public immutable ohm;
2022-08-olympus/src/modules/RANGE.sol::71 => ERC20 public immutable reserve;
2022-08-olympus/src/policies/Operator.sol::82 => ERC20 public immutable ohm;
2022-08-olympus/src/policies/Operator.sol::83 => uint8 public immutable ohmDecimals;
2022-08-olympus/src/policies/Operator.sol::85 => ERC20 public immutable reserve;
2022-08-olympus/src/policies/Operator.sol::86 => uint8 public immutable reserveDecimals;
```

## [G-05] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-08-olympus/src/Kernel.sol::76 => function changeKernel(Kernel newKernel_) external onlyKernel {
2022-08-olympus/src/Kernel.sol::105 => function INIT() external virtual onlyKernel {}
2022-08-olympus/src/Kernel.sol::126 => function setActiveStatus(bool activate_) external onlyKernel {
2022-08-olympus/src/Kernel.sol::235 => function executeAction(Actions action_, address target_) external onlyExecutor {
2022-08-olympus/src/Kernel.sol::439 => function grantRole(Role role_, address addr_) public onlyAdmin {
2022-08-olympus/src/Kernel.sol::451 => function revokeRole(Role role_, address addr_) public onlyAdmin {
2022-08-olympus/src/policies/BondCallback.sol::152 => function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {
2022-08-olympus/src/policies/BondCallback.sol::190 => function setOperator(Operator operator_) external onlyRole("callback_admin") {
2022-08-olympus/src/policies/Heart.sol::130 => function resetBeat() external onlyRole("heart_admin") {
2022-08-olympus/src/policies/Heart.sol::135 => function toggleBeat() external onlyRole("heart_admin") {
2022-08-olympus/src/policies/Heart.sol::150 => function withdrawUnspentRewards(ERC20 token_) external onlyRole("heart_admin") {
2022-08-olympus/src/policies/Operator.sol::195 => function operate() external override onlyWhileActive onlyRole("operator_operate") {
2022-08-olympus/src/policies/Operator.sol::510 => function setThresholdFactor(uint256 thresholdFactor_) external onlyRole("operator_policy") {
2022-08-olympus/src/policies/Operator.sol::516 => function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {
2022-08-olympus/src/policies/Operator.sol::548 => function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {
2022-08-olympus/src/policies/Operator.sol::618 => function regenerate(bool high_) external onlyRole("operator_admin") {
2022-08-olympus/src/policies/Operator.sol::624 => function toggleActive() external onlyRole("operator_admin") {
2022-08-olympus/src/policies/VoterRegistration.sol::45 => function issueVotesTo(address wallet_, uint256 amount_) external onlyRole("voter_admin") {
2022-08-olympus/src/policies/VoterRegistration.sol::53 => function revokeVotesFrom(address wallet_, uint256 amount_) external onlyRole("voter_admin") {
```

## [G-06] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
2022-08-olympus/src/Kernel.sol::85 => constructor(Kernel kernel_) KernelAdapter(kernel_) {}
2022-08-olympus/src/Kernel.sol::95 => function KEYCODE() public pure virtual returns (Keycode) {}
2022-08-olympus/src/Kernel.sol::100 => function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}
2022-08-olympus/src/Kernel.sol::105 => function INIT() external virtual onlyKernel {}
2022-08-olympus/src/Kernel.sol::115 => constructor(Kernel kernel_) KernelAdapter(kernel_) {}
2022-08-olympus/src/Kernel.sol::139 => function configureDependencies() external virtual returns (Keycode[] memory dependencies) {}
2022-08-olympus/src/Kernel.sol::143 => function requestPermissions() external view virtual returns (Permissions[] memory requests) {}
2022-08-olympus/src/modules/INSTR.sol::20 => constructor(Kernel kernel_) Module(kernel_) {}
2022-08-olympus/src/modules/TRSRY.sol::45 => constructor(Kernel kernel_) Module(kernel_) {}
2022-08-olympus/src/modules/VOTES.sol::19 => {}
2022-08-olympus/src/policies/Governance.sol::59 => constructor(Kernel kernel_) Policy(kernel_) {}
2022-08-olympus/src/policies/PriceConfig.sol::15 => constructor(Kernel kernel_) Policy(kernel_) {}
2022-08-olympus/src/policies/TreasuryCustodian.sol::24 => constructor(Kernel kernel_) Policy(kernel_) {}
2022-08-olympus/src/policies/VoterRegistration.sol::16 => constructor(Kernel kernel_) Policy(kernel_) {}
```

## [G-07] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2022-08-olympus/src/modules/PRICE.sol::44 => uint32 public nextObsIndex;
2022-08-olympus/src/modules/PRICE.sol::47 => uint32 public numObservations;
2022-08-olympus/src/modules/PRICE.sol::59 => uint8 public constant decimals = 18;
2022-08-olympus/src/modules/PRICE.sol::84 => uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();
2022-08-olympus/src/modules/PRICE.sol::87 => uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();
2022-08-olympus/src/modules/PRICE.sol::127 => uint32 numObs = numObservations;
2022-08-olympus/src/modules/PRICE.sol::185 => uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;
2022-08-olympus/src/policies/Operator.sol::83 => uint8 public immutable ohmDecimals;
2022-08-olympus/src/policies/Operator.sol::86 => uint8 public immutable reserveDecimals;
2022-08-olympus/src/policies/Operator.sol::89 => uint32 public constant FACTOR_SCALE = 1e4;
2022-08-olympus/src/policies/Operator.sol::485 => int8 decimals;
2022-08-olympus/src/policies/Operator.sol::665 => uint32 observe = _config.regenObserve;
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
```

## [G-08] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2022-08-olympus/src/Kernel.sol::113 => bool public isActive;
2022-08-olympus/src/Kernel.sol::181 => mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions;
2022-08-olympus/src/Kernel.sol::194 => mapping(address => mapping(Role => bool)) public hasRole;
2022-08-olympus/src/Kernel.sol::197 => mapping(Role => bool) public isRole;
2022-08-olympus/src/modules/PRICE.sol::62 => bool public initialized;
2022-08-olympus/src/policies/BondCallback.sol::24 => mapping(address => mapping(uint256 => bool)) public approvedMarkets;
2022-08-olympus/src/policies/Governance.sol::105 => mapping(uint256 => bool) public proposalHasBeenActivated;
2022-08-olympus/src/policies/Governance.sol::117 => mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;
2022-08-olympus/src/policies/Heart.sol::33 => bool public active;
2022-08-olympus/src/policies/Operator.sol::63 => bool public initialized;
2022-08-olympus/src/policies/Operator.sol::66 => bool public active;
```

## [G-09] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-08-olympus/src/modules/PRICE.sol::136 => _movingAverage += (currentPrice - earliestPrice) / numObs;
2022-08-olympus/src/modules/PRICE.sol::138 => _movingAverage -= (earliestPrice - currentPrice) / numObs;
2022-08-olympus/src/modules/PRICE.sol::222 => total += startObservations_[i];
2022-08-olympus/src/modules/TRSRY.sol::96 => reserveDebt[token_][msg.sender] += amount_;
2022-08-olympus/src/modules/TRSRY.sol::97 => totalDebt[token_] += amount_;
2022-08-olympus/src/modules/TRSRY.sol::115 => reserveDebt[token_][msg.sender] -= received;
2022-08-olympus/src/modules/TRSRY.sol::116 => totalDebt[token_] -= received;
2022-08-olympus/src/modules/TRSRY.sol::131 => if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;
2022-08-olympus/src/modules/TRSRY.sol::132 => else totalDebt[token_] -= oldDebt - amount_;
2022-08-olympus/src/modules/VOTES.sol::56 => balanceOf[from_] -= amount_;
2022-08-olympus/src/modules/VOTES.sol::58 => balanceOf[to_] += amount_;
2022-08-olympus/src/policies/BondCallback.sol::143 => _amountsPerMarket[id_][0] += inputAmount_;
2022-08-olympus/src/policies/BondCallback.sol::144 => _amountsPerMarket[id_][1] += outputAmount_;
2022-08-olympus/src/policies/Governance.sol::194 => totalEndorsementsForProposal[proposalId_] -= previousEndorsement;
2022-08-olympus/src/policies/Governance.sol::198 => totalEndorsementsForProposal[proposalId_] += userVotes;
2022-08-olympus/src/policies/Governance.sol::252 => yesVotesForProposal[activeProposal.proposalId] += userVotes;
2022-08-olympus/src/policies/Governance.sol::254 => noVotesForProposal[activeProposal.proposalId] += userVotes;
2022-08-olympus/src/policies/Heart.sol::103 => lastBeat += frequency();
```

## [G-11] Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
2022-08-olympus/src/policies/interfaces/IHeart.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus/src/policies/interfaces/IOperator.sol::2 => pragma solidity >=0.8.0;
```

## [G-12] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2022-08-olympus/src/utils/KernelUtils.sol::49 => i++;
2022-08-olympus/src/utils/KernelUtils.sol::64 => i++;
```

## [G-13] Don't compare boolean expressions to boolean literals

The extran comparison wastes gas
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

```
2022-08-olympus/src/policies/Governance.sol::223 => if (proposalHasBeenActivated[proposalId_] == true) {
2022-08-olympus/src/policies/Governance.sol::306 => if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {
```

## [G-14] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-08-olympus/src/Kernel.sol::439 => function grantRole(Role role_, address addr_) public onlyAdmin {
2022-08-olympus/src/Kernel.sol::451 => function revokeRole(Role role_, address addr_) public onlyAdmin {
2022-08-olympus/src/modules/INSTR.sol::28 => function VERSION() public pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/INSTR.sol::37 => function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {
2022-08-olympus/src/modules/MINTR.sol::20 => function KEYCODE() public pure override returns (Keycode) {
2022-08-olympus/src/modules/MINTR.sol::33 => function mintOhm(address to_, uint256 amount_) public permissioned {
2022-08-olympus/src/modules/MINTR.sol::37 => function burnOhm(address from_, uint256 amount_) public permissioned {
2022-08-olympus/src/modules/PRICE.sol::108 => function KEYCODE() public pure override returns (Keycode) {
2022-08-olympus/src/modules/RANGE.sol::110 => function KEYCODE() public pure override returns (Keycode) {
2022-08-olympus/src/modules/TRSRY.sol::47 => function KEYCODE() public pure override returns (Keycode) {
2022-08-olympus/src/modules/VOTES.sol::22 => function KEYCODE() public pure override returns (Keycode) {
2022-08-olympus/src/modules/VOTES.sol::45 => function transfer(address to_, uint256 amount_) public pure override returns (bool) {
2022-08-olympus/src/policies/Governance.sol::145 => function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {
2022-08-olympus/src/policies/Governance.sol::151 => function getActiveProposal() public view returns (ActivatedProposal memory) {
```

## [G-15] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2022-08-olympus/src/modules/INSTR.sol::28 => function VERSION() public pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/MINTR.sol::25 => function VERSION() external pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/PRICE.sol::113 => function VERSION() external pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/RANGE.sol::115 => function VERSION() external pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/TRSRY.sol::51 => function VERSION() external pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/modules/VOTES.sol::27 => function VERSION() external pure override returns (uint8 major, uint8 minor) {
2022-08-olympus/src/policies/BondCallback.sol::177 => returns (uint256 in_, uint256 out_)
```

## [G-16] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2022-08-olympus/src/modules/TRSRY.sol::33 => mapping(address => mapping(ERC20 => uint256)) public withdrawApproval;
2022-08-olympus/src/modules/TRSRY.sol::39 => mapping(ERC20 => mapping(address => uint256)) public reserveDebt;
```

```
2022-08-olympus/src/policies/Governance.sol::102 => mapping(uint256 => mapping(address => uint256)) public userEndorsementsForProposal;
2022-08-olympus/src/policies/Governance.sol::114 => mapping(uint256 => mapping(address => uint256)) public userVotesForProposal;
2022-08-olympus/src/policies/Governance.sol::117 => mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;
```

## [G-17] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
2022-08-olympus/src/Kernel.sol::269 => if (address(getModuleForKeycode[keycode]) != address(0))
```

## [G-18] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```
2022-08-olympus/src/Kernel.sol::379 => Policy[] memory dependents = moduleDependents[keycode_];
```

## [G-19] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2022-08-olympus/src/Kernel.sol::131 => function getModuleAddress(Keycode keycode_) internal view returns (address) {
2022-08-olympus/src/Kernel.sol::266 => function _installModule(Module newModule_) internal {
2022-08-olympus/src/Kernel.sol::279 => function _upgradeModule(Module newModule_) internal {
2022-08-olympus/src/Kernel.sol::295 => function _activatePolicy(Policy policy_) internal {
2022-08-olympus/src/Kernel.sol::325 => function _deactivatePolicy(Policy policy_) internal {
2022-08-olympus/src/Kernel.sol::351 => function _migrateKernel(Kernel newKernel_) internal {
2022-08-olympus/src/Kernel.sol::378 => function _reconfigurePolicies(Keycode keycode_) internal {
2022-08-olympus/src/Kernel.sol::409 => function _pruneFromDependents(Policy policy_) internal {
2022-08-olympus/src/policies/Heart.sol::111 => function _issueReward(address to_) internal {
2022-08-olympus/src/policies/Operator.sol::652 => function _addObservation() internal {
```

## [G-20] State variables only set in the constructor should be declared immutable

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

```
2022-08-olympus/src/policies/BondCallback.sol::43 => aggregator = aggregator_;
2022-08-olympus/src/policies/BondCallback.sol::44 => ohm = ohm_;
2022-08-olympus/src/policies/Heart.sol::60 => _operator = operator_;
2022-08-olympus/src/policies/Operator.sol::144 => _status = Status({low: regen, high: regen});
```

## [G-21] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2022-08-olympus/src/Kernel.sol::131 => function getModuleAddress(Keycode keycode_) internal view returns (address) {
```
