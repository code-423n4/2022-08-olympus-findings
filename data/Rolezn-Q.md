## (1) Init functions are susceptible to front-running

Severity: Low

Most contracts use an init pattern (instead of a constructor) to initialize contract parameters. Unless these are enforced to be atomic with contact deployment via deployment script or factory contracts, they are susceptible to front-running race conditions where an attacker/griefer can front-run (cannot access control because admin roles are not initialized) to initially with their own (malicious) parameters upon detecting (if an event is emitted) which the contract deployer has to redeploy wasting gas and risking other transactions from interacting with the attacker-initialized contract.

Many init functions do not have an explicit event emission which makes monitoring such scenarios harder. All of them have re-init checks; while many are explicit some (those in auction contracts) have implicit reinit checks in initAccessControls() which is better if converted to an explicit check in the main init function itself.
(details credit to: code-423n4/2021-09-sushimiso-findings#64)

## Proof Of Concept

	function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L205

	function initialize() external onlyRole("operator_admin") {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L598

	function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L45



## Recommended Mitigation Steps

Ensure atomic calls to init functions along with deployment via robust deployment scripts or factory contracts. Emit explicit events for initializations of contracts. Enfore prevention of re-initializations via explicit setting/checking of boolean initialized variables in the main init function instead of downstream function checks.



## (2) Use _safeMint instead of _mint

Severity: Low

According to openzepplin's ERC721, the use of _mint is discouraged, use _safeMint whenever possible.
https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-_mint-address-uint256-

## Proof Of Concept


	_mint(wallet_, amount_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L36



## Recommended Mitigation Steps

Use _safeMint whenever possible instead of _mint



## (3) Missing Checks for Address(0x0) 

Severity: Low

## Proof Of Concept


	function _issueReward(address to_) internal {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L111



## Recommended Mitigation Steps

Consider adding zero-address checks in the mentioned codebase.



## (4) Safeapprove() Is Deprecated

Severity: Low

Deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead

## Proof Of Concept


	ohm.safeApprove(address(MINTR), type(uint256).max);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L57

	ohm.safeApprove(address(MINTR), type(uint256).max);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L167



## Recommended Mitigation Steps

Consider using safeIncreaseAllowance() / safeDecreaseAllowance() instead.



## (5) Use Safetransfer Instead Of Transfer 

Severity: Low

It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

Reference: This similar medium-severity (https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call) finding from Consensys Diligence Audit of Fei Protocol.

## Proof Of Concept


	VOTES.transferFrom(msg.sender, address(this), userVotes);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L259

	VOTES.transferFrom(address(this), msg.sender, userVotes);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L312



## Recommended Mitigation Steps

Consider using safeTransfer/safeTransferFrom or require() consistently.



## (6) Variable Names That Consist Of All Capital Letters Should Be Reserved For Const/immutable Variables

Severity: Non-Critical

If the variable needs to be different based on which class it comes from, a view/pure function should be used instead.

## Proof Of Concept

	Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L68

	Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L35


## (7) Constants Should Be Defined Rather Than Using Magic Numbers

Severity: Non-Critical

## Proof Of Concept
    wallSpread_ > 10000 ||
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L245

    if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L264

    (totalEndorsementsForProposal[proposalId_] * 100) <
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L217

    while (price_ >= 10) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L486

    if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L518

    if (duration_ > uint256(7 days) || duration_ < uint256(1 days))
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L533

    if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L550

    if (wait_ < 1 hours || threshold_ > observe_ || observe_ == 0)
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L565


## (8) Event Is Missing Indexed Fields

Severity: Non-Critical

Each event should use three indexed fields if there are three or more fields.
In addition, each event should have at least one indexed fields to allow easier filtering of logs.

## Proof Of Concept


	event PermissionsUpdated(
        Keycode indexed keycode_,
        Policy indexed policy_,
        bytes4 funcSelector_,
        bool granted_
    );
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L203

	event NewObservation(uint256 timestamp_, uint256 price_, uint256 movingAverage_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L26

	event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L20

	event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L21

	event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L22

	event CushionDown(bool high_, uint256 timestamp_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L23

	event PricesChanged(
        uint256 wallLowPrice_,
        uint256 cushionLowPrice_,
        uint256 cushionHighPrice_,
        uint256 wallHighPrice_
    );
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L24

	event SpreadsChanged(uint256 cushionSpread_, uint256 wallSpread_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L30

	event ApprovedForWithdrawal(address indexed policy_, ERC20 indexed token_, uint256 amount_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L20

	event DebtIncurred(ERC20 indexed token_, address indexed policy_, uint256 amount_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L27

	event DebtRepaid(ERC20 indexed token_, address indexed policy_, uint256 amount_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L28

	event DebtSet(ERC20 indexed token_, address indexed policy_, uint256 amount_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L29

	event ProposalEndorsed(uint256 proposalId, address voter, uint256 amount);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L87

	event ProposalActivated(uint256 proposalId, uint256 timestamp);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L88

	event WalletVoted(uint256 proposalId, address voter, bool for_, uint256 userVotes);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L89

	event RewardIssued(address to_, uint256 rewardAmount_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L29

	event RewardUpdated(ERC20 token_, uint256 rewardAmount_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L30

	event Swap(
        ERC20 indexed tokenIn_,
        ERC20 indexed tokenOut_,
        uint256 amountIn_,
        uint256 amountOut_
    );
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L45

	event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L52

	event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L54



## (9) Missing event for critical parameter change

Severity: Non-Critical

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

## Proof Of Concept


	function changeKernel(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L76

	function setActiveStatus(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L126

	function setOperator(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L190

	function setSpreads(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L498

	function setThresholdFactor(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L510

	function setBondContracts(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L586

	function changeMovingAverageDuration(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L58

	function changeObservationFrequency(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L69



## (10) Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

Severity: Non-Critical

## Proof Of Concept


	contract Kernel 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L149

	contract OlympusInstructions is Module 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/INSTR.sol#L10

	contract OlympusMinter is Module 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L8

	contract OlympusPrice is Module 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L22

	contract OlympusRange is Module 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L16

	contract OlympusTreasury is Module, ReentrancyGuard 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L17

	contract OlympusVotes is Module, ERC20 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L11

	contract BondCallback is Policy, ReentrancyGuard, IBondCallback 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L17

	contract OlympusGovernance is Policy 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L51

	contract OlympusHeart is IHeart, Policy, ReentrancyGuard 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L21

	contract Operator is IOperator, Policy, ReentrancyGuard 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L30

	contract OlympusPriceConfig is Policy 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L7

	contract TreasuryCustodian is Policy 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L15

	contract VoterRegistration is Policy 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/VoterRegistration.sol#L9



## (11) Use a more recent version of Solidity

Severity: Non-Critical

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

## Proof Of Concept


	Found old version 0.8.0 of Solidity
https://github.com/code-423n4/2022-08-olympus/tree/main/src/interfaces/IBondCallback.sol#L2

	Found old version 0.8.0 of Solidity
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IHeart.sol#L2

	Found old version 0.8.0 of Solidity
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/interfaces/IOperator.sol#L2

	Found old version 0.8.0 of Solidity 
https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/lib/bonds/interfaces/IBondCallback.sol#L2



## Recommended Mitigation Steps

Consider updating to a more recent solidity version.



## (12) Public Functions Not Called By The Contract Should Be Declared External Instead

Severity: Non-Critical

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

## Proof Of Concept


	function grantRole(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L439

	function revokeRole(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L451

	function mintOhm(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L33

	function burnOhm(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L37

	function updateMarket(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L215

	function withdrawReserves(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L75

	function transferFrom(
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L51



## (13) Large multiples of ten should use scientific notation

Severity: Non-Critical

Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

## Proof Of Concept


            wallSpread_ > 10000 ||
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L245

            cushionSpread_ > 10000 ||
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L247

        if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L264

            wallSpread_ > 10000 ||
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L245

            cushionSpread_ > 10000 ||
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L247

        if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L264

            wallSpread_ > 10000 ||
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L245

            cushionSpread_ > 10000 ||
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L247

        if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L264

        if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L164

        if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L111

        if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L518

        if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L550

        if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L111

        if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L518

        if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L550

        if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L111

        if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L518

        if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L550



## (14) Use of Block.Timestamp

Severity: Non-Critical

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

## Proof Of Concept


    lastObservationTime = uint48(block.timestamp);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L143

    if (updatedAt < block.timestamp - 3 * uint256(observationFrequency))
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L165

    if (updatedAt < block.timestamp - uint256(observationFrequency))
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L171

    if (startObservations_.length != numObs || lastObservationTime_ > uint48(block.timestamp))
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L215

    lastActive: uint48(block.timestamp),
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L85

    _range.high.lastActive = uint48(block.timestamp);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L136

    _range.low.lastActive = uint48(block.timestamp);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L148

    getProposalMetadata[proposalId] = ProposalMetadata(
            title_,
            msg.sender,
            block.timestamp,
            proposalURI_
        );
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L168-L173

    if (block.timestamp > proposal.submissionTimestamp + ACTIVATION_DEADLINE) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L212

    if (block.timestamp < activeProposal.activationTimestamp + GRACE_PERIOD) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L227

    activeProposal = ActivatedProposal(proposalId_, block.timestamp);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L231

    if (block.timestamp < activeProposal.activationTimestamp + EXECUTION_TIMELOCK) {
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L272

    lastBeat = block.timestamp;
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L63

    if (block.timestamp < lastBeat + frequency()) revert Heart_OutOfCycle();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L94

    lastBeat = block.timestamp - frequency();
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L131

    lastRegen: uint48(block.timestamp),
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L128

    uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L210

    uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L216

    conclusion: uint48(block.timestamp + config_.cushionDuration),
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L404

    _status.high.lastRegen = uint48(block.timestamp);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L708

    _status.low.lastRegen = uint48(block.timestamp);
https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L720



## Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



