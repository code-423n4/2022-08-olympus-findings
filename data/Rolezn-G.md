## (1) <Array>.length Should Not Be Looked Up In Every Loop Of A For-loop

Severity: Gas Optimizations

The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

## Proof Of Concept

	for (uint256 step; step < instructions.length; ) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L278



## (2) Using Calldata Instead Of Memory For Read-only Arguments In External Functions Saves Gas

Severity: Gas Optimizations

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one


## Proof Of Concept


	function configureDependencies() external virtual returns (Keycode[] memory dependencies) {}
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L139

	function requestPermissions() external view virtual returns (Permissions[] memory requests) {}
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L143

	function _setPolicyPermissions(
        Policy policy_,
        Permissions[] memory requests_,
        bool grant_
    ) internal {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L391

	function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/INSTR.sol#L37

	function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L205

	function configureDependencies() external override returns (Keycode[] memory dependencies) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L48

	function requestPermissions()
        external
        view
        override
        onlyKernel
        returns (Permissions[] memory requests)
    {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L61

	function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L152

	function configureDependencies() external override returns (Keycode[] memory dependencies) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L61

	function requestPermissions()
        external
        view
        override
        onlyKernel
        returns (Permissions[] memory requests)
    {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L70

	function configureDependencies() external override returns (Keycode[] memory dependencies) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L69

	function requestPermissions()
        external
        view
        override
        returns (Permissions[] memory permissions)
    {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L77

	function configureDependencies() external override returns (Keycode[] memory dependencies) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L154

	function requestPermissions() external view override returns (Permissions[] memory requests) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L171

	function configureDependencies() external override returns (Keycode[] memory dependencies) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L18

	function requestPermissions()
        external
        view
        override
        returns (Permissions[] memory permissions)
    {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L25

	function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L45

	function configureDependencies() external override returns (Keycode[] memory dependencies) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L27

	function requestPermissions() external view override returns (Permissions[] memory requests) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L34

	function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L53

	function configureDependencies() external override returns (Keycode[] memory dependencies) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/VoterRegistration.sol#L19

	function requestPermissions()
        external
        view
        override
        returns (Permissions[] memory permissions)
    {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/VoterRegistration.sol#L27


## (3) Empty Blocks Should Be Removed Or Emit Something

Severity: Gas Optimizations

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

## Proof Of Concept

	constructor(Kernel kernel_) KernelAdapter(kernel_) {}
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L85

	function KEYCODE() public pure virtual returns (Keycode) {}
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L95

	function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L100

	function INIT() external virtual onlyKernel {}
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L105

	function configureDependencies() external virtual returns (Keycode[] memory dependencies) {}
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L139

	function requestPermissions() external view virtual returns (Permissions[] memory requests) {}
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L143



## (4) It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied

Severity: Gas Optimizations

## Proof Of Concept


	for (uint256 i = 0; i < reqLength; ) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L397




## (5) Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate

Severity: Gas Optimizations

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

## Proof Of Concept


	mapping(address => mapping(ERC20 => uint256)) public withdrawApproval;
	mapping(address => uint256)) public reserveDebt;

https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L33

	mapping(address => uint256)) public userEndorsementsForProposal;
	mapping(address => uint256)) public userVotesForProposal;
	mapping(address => bool)) public tokenClaimsForProposal;

https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L102






## (6) Multiplication/division By Two Should Use Bit Shifting

Severity: Gas Optimizations

<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

## Proof Of Concept


	int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L372

	uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L419

	uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L420

	int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L427

	) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L786





## (7) Not Using The Named Return Variables When A Function Returns, Wastes Deployment Gas

Severity: Gas Optimizations

## Proof Of Concept

	function amountsForMarket(uint256 id_)
        external
        view
        override
        returns (uint256 in_, uint256 out_)
    {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L173


## (8) <x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables

Severity: Gas Optimizations

## Proof Of Concept


	_movingAverage += (currentPrice - earliestPrice) / numObs;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L136

	_movingAverage -= (earliestPrice - currentPrice) / numObs;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L138

	total += startObservations_[i];
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L222

	reserveDebt[token_][msg.sender] += amount_;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L96

	totalDebt[token_] += amount_;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L97

	reserveDebt[token_][msg.sender] -= received;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L115

	totalDebt[token_] -= received;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L116

	if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L131

	else totalDebt[token_] -= oldDebt - amount_;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L132

	balanceOf[from_] -= amount_;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L56

	balanceOf[to_] += amount_;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L58

	_amountsPerMarket[id_][0] += inputAmount_;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L143

	_amountsPerMarket[id_][1] += outputAmount_;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L144

	totalEndorsementsForProposal[proposalId_] -= previousEndorsement;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L194

	totalEndorsementsForProposal[proposalId_] += userVotes;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L198

	yesVotesForProposal[activeProposal.proposalId] += userVotes;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L252

	noVotesForProposal[activeProposal.proposalId] += userVotes;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L254

	lastBeat += frequency();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L103





## (9) Using Private Rather Than Public For Constants, Saves Gas

Severity: Gas Optimizations

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

## Proof Of Concept



	uint8 public constant decimals = 18;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L59


	uint256 public constant FACTOR_SCALE = 1e4;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L65


	uint256 public constant SUBMISSION_REQUIREMENT = 100;
	uint256 public constant ACTIVATION_DEADLINE = 2 weeks;
	uint256 public constant GRACE_PERIOD = 1 weeks;
	uint256 public constant ENDORSEMENT_THRESHOLD = 20;
	uint256 public constant EXECUTION_THRESHOLD = 33;
	uint256 public constant EXECUTION_TIMELOCK = 3 days;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L121-L137


	uint32 public constant FACTOR_SCALE = 1e4;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L89



## Recommended Mitigation Steps

Set variable to private.



## (10) Public Functions To External

Severity: Gas Optimizations

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

## Proof Of Concept


	function KEYCODE() public pure virtual returns (Keycode) {}
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L95

	function grantRole(Role role_, address addr_) public onlyAdmin {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L439

	function revokeRole(Role role_, address addr_) public onlyAdmin {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L451

	function KEYCODE() public pure override returns (Keycode) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/INSTR.sol#L23

	function VERSION() public pure override returns (uint8 major, uint8 minor) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/INSTR.sol#L28

	function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/INSTR.sol#L37

	function KEYCODE() public pure override returns (Keycode) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L20

	function mintOhm(address to_, uint256 amount_) public permissioned {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L33

	function burnOhm(address from_, uint256 amount_) public permissioned {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L37

	function KEYCODE() public pure override returns (Keycode) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L108

	function getCurrentPrice() public view returns (uint256) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L154

	function KEYCODE() public pure override returns (Keycode) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L110

	function updateMarket(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L215

	function KEYCODE() public pure override returns (Keycode) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L47

	function withdrawReserves(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L75

	function KEYCODE() public pure override returns (Keycode) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L22

	function transfer(address to_, uint256 amount_) public pure override returns (bool) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L45

	function transferFrom(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L51

	function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L145

	function getActiveProposal() public view returns (ActivatedProposal memory) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L151

	function frequency() public view returns (uint256) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L121

	function getAmountOut(ERC20 tokenIn_, uint256 amountIn_) public view returns (uint256) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L749

	function fullCapacity(bool high_) public view override returns (uint256) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L778






## (11) Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It

Severity: Gas Optimizations

To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

## Proof Of Concept


	if (address(getModuleForKeycode[keycode]) != address(0))
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L269

	Module oldModule = getModuleForKeycode[keycode];
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L281

	moduleDependents[keycode].push(policy_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L309

	getDependentIndex[keycode][policy_] = moduleDependents[keycode].length - 1;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L310

	Policy[] storage dependents = moduleDependents[keycode];
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L415

	uint256 origIndex = getDependentIndex[keycode][policy_];
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L417

	delete getDependentIndex[keycode][policy_];
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L426

	Instruction[] storage instructions = storedInstructions[instructionsId];
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/INSTR.sol#L46

	uint256 earliestPrice = observations[nextObsIndex];
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L130

	return observations[lastIndex];
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L186

	kernel.executeAction(instructions[step].action, instructions[step].target);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L279






## (12) Use of non-uint64 state variable is less efficient than uint256

Severity: Gas Optimizations

Lower than uint256 size storage variables are less gas efficient.
Using uint64 does not give any efficiency, actually, it is the opposite as EVM operates on default of 256-bit values so uint64 is more expensive in this case as it needs a conversion. It only gives improvements in cases where you can pack variables together, e.g. structs.

## Proof Of Concept


	uint32 public nextObsIndex;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L44

	uint32 public numObservations;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L47

	uint48 public observationFrequency;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L50

	uint48 public movingAverageDuration;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L53

	uint48 public lastObservationTime;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L56

	uint8 public constant decimals = 18;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L59

	uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L84

	uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L87

	uint32 numObs = numObservations;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L127

	uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L185

	uint48 lastActive;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L45

	uint8 public immutable ohmDecimals;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L83

	uint8 public immutable reserveDecimals;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L86

	uint32 public constant FACTOR_SCALE = 1e4;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L89

	uint8 oracleDecimals = PRICE.decimals();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L418

	uint32 observe = _config.regenObserve;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L665

	uint32 cushionFactor;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L13

	uint32 cushionDuration;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L14

	uint32 cushionDebtBuffer;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L15

	uint32 cushionDepositInterval;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L16

	uint32 reserveFactor;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L17

	uint32 regenWait;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L18

	uint32 regenThreshold;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L19

	uint32 regenObserve;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L20

	uint32 count;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L31

	uint48 lastRegen;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L32

	uint32 nextObservation;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L33



## (13) Using Bools For Storage Incurs Overhead

Severity: Gas Optimizations

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

## Proof Of Concept


	bool public isActive;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L113

    mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L181

    mapping(address => mapping(Role => bool)) public hasRole;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L194

    mapping(Role => bool) public isRole;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L197

	bool public initialized;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L62

    mapping(address => mapping(uint256 => bool)) public approvedMarkets;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L24

    mapping(uint256 => bool) public proposalHasBeenActivated;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L105

    mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L117

	bool public active;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L33

	bool public initialized;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L63

	bool public active;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L66





