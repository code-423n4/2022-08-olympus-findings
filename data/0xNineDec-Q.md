<h3>Low Risk Issues</h3> 

|       | Title                                                                                                            | N° of Appearances |
| :---: | :--------------------------------------------------------------------------------------------------------------- | :---------------: |
| [L-1] | Unbounded array length on for loop                                                                               |         4         |
| [L-2] | Insufficient protection of sensitive data                                                                        |         1         |
| [L-3] | No Zero Address checks performed when assigning address values                                                   |         5         |
| [L-4] | Important state changes should emit events                                                                       |         6         |
| [L-5] | Open TODOs across the codebase                                                                                   |         2         |
| [L-6] | The allowed role has too much power over some actions                                                            |         2         |
| [L-7] | A malicious admin can pair <code>Heart.resetBeat()</code> along with <code>Heart.beat()</code> and steal rewards |         1         |
| [L-8] | Lack of boolean return check on transfer or transferFrom                                                         |         2         |

<em>Total: 23 appearances over 8 issues.</em> 


<h3>Non-critical Risk Issues</h3> 

|       | Title                                                                                       | N° of Appearances |
| :---: | :------------------------------------------------------------------------------------------ | :---------------: |
| [N-1] | No indexed parameters while emitting events                                                 |         13        |
| [N-2] | Public functions that are not called by the contract should be external                     |         10        |
| [N-3] | Constants should be declared instead of magic numbers                                       |         11        |
| [N-4] | Repeated <code>require</code> statements or gate controls could be refactored to a modifier |         4         |
| [N-5] | Change token name on deployment                                                             |         1         |
| [N-6] | Function or public variable with missing or incomplete natspec                              |         19        |
| [N-7] | Inconsistent way of declaring the pragma version                                            |         3         |
| [N-8] | Capped variable names should be reserved for constant or immutable variables                |         3         |
| [N-9] | Usage of <code>isContract</code> or similar                                                 |         1         |

<em>Total: 65 appearances over 9 issues.</em> 

 <h2>Low Risk Issues</h2> 
<h3> [L-1] Unbounded array length on for loop </h3> 
Providing unbounded arrays that could come from function inputs or local logic may cause a loop to exceed the max. block gas usage reverting the whole call. Whenever it is possible, it is advised to hard-cap the length of the arrays and check their length before looping in order to prevent wasting unnecessary gas on execution.<br><br><em>Found 4 times</em>

```solidity
INSTR.sol   L42:       function store(Instruction[] calldata instructions_) external permissioned returns (uint256) {
```
```solidity
Kernel.sol   L391:       function _setPolicyPermissions(
Kernel.sol   L409:       function _pruneFromDependents(Policy policy_) internal {
```
```solidity
BondCallback.sol   L152:       function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {
```
<br><h3> [L-2] Insufficient protection of sensitive data </h3> 
The <code>hardhat.config.ts</code> uses sensitive information imported from an un-committed environment file. The usage of either <code>.env</code> imported variables or even plain pasted keys make it easier for an attacker to compromise the keys used for monitoring, deployment, testing and even if wallet private keys are used in such way funds can be compromised. It is advised to migrate sensitive data storage to a more secure system such as hardware devices. The following data could be compromised if a leak happens or if the <code>.gitignore</code> file is mistakenly deleted according to the imports performed on <code>hardhat.config.ts</code>: <br><br><em>Found 1 time</em>

```solidity
# Network config
RPC_URL=
CHAIN=

# Deployment verification
ETHERSCAN_KEY=

# Deployer account
PRIVATE_KEY=
ETH_FROM=

# Guardian account
GUARDIAN_ADDRESS=
GUARDIAN_PRIVATE_KEY=

# Policy account
POLICY_ADDRESS=
POLICY_PRIVATE_KEY=     
```
<br><h3> [L-3] No Zero Address checks performed when assigning address values </h3> 
It is debatable if it is really needed to check that an address is not the <code>address(0)</code> while setting values for state variables. Based on past attacks or leaks that where done because of a lack of checks while setting up address values, it is advised to perform a zero address check on the following cases:<br><br><em>Found 5 times</em>

```solidity
Kernel.sol   L66:       kernel = kernel_;
Kernel.sol   L77:       kernel = newKernel_;
Kernel.sol   L251:       executor = target_;
```
```solidity
BondCallback.sol   L40:       IBondAggregator aggregator_,
BondCallback.sol   L41:       ERC20 ohm_
```
<br><h3> [L-4] Important state changes should emit events </h3> 
In favor of bringing transparency and increasing the trust on the protocol, it is advised to emit events while performing important state changes such as parameter changes, token flows, actions performed by the roles with higher clearance, among others. Notifying the community and offchain filtering services by emitting indexed events is recommended while performing the following actions: <br><br><em>Found 6 times</em>

```solidity
Kernel.sol   L127:       isActive = activate_;
```
```solidity
Operator.sol   L586:       function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_)
Operator.sol   L624:       function toggleActive() external onlyRole("operator_admin") {
```
```solidity
Heart.sol   L130:       function resetBeat() external onlyRole("heart_admin") {
Heart.sol   L135:       function toggleBeat() external onlyRole("heart_admin") {
Heart.sol   L150:       function withdrawUnspentRewards(ERC20 token_) external onlyRole("heart_admin") {
```
<br><h3> [L-5] Open TODOs across the codebase </h3> 
Having open TODO comments across the codebase not only decrease its quality but also may show non implemented features which could provide a leak of possible attack vectors. It is advised to resolve every single TODO before deploying. For the record, any implementation or feature that may result from resolving an open TODO will be out of the scope of this audit and the plausible impacts that may derive from their implementations may require further audits to be determined.<br><br><em>Found 2 times</em>

```solidity
TreasuryCustodian.sol   L51:       /* TODO Currently allows anyone to revoke any approval EXCEPT activated policies. */
TreasuryCustodian.sol   L52:       /* TODO must reorg policy storage to be able to check for deactivated policies.*/
```
<br><h3> [L-6] The allowed role has too much power over some actions </h3> 
There are some functions that have considerable impact within the community that compromise de decentralization of the protocol and deposit too much trust on the approved caller of that function. In order to build trustless protocols, there should not be a role holding too much power or whose decisions have breaking impact on the community.<br><br><em>Found 2 times</em>

```solidity
VoterRegistration.sol   L45:       function issueVotesTo(address wallet_, uint256 amount_) external onlyRole("voter_admin") {
VoterRegistration.sol   L53:       function revokeVotesFrom(address wallet_, uint256 amount_) external onlyRole("voter_admin") {
```
<br><h3> [L-7] A malicious admin can pair <code>Heart.resetBeat()</code> along with <code>Heart.beat()</code> and steal rewards </h3> 
A malicious admin can create a bundled transaction that calls <code>Heart.resetBeat()</code> and then <code>Heart.beat()</code> within the same bundle or pair two transactions from different accounts (reset it from the admin account and beat from a foreign account) and keep all the rewards to himself.<br><br><em>Found 1 time</em>

```solidity
Heart.sol   L131:       lastBeat = block.timestamp - frequency();
```
<br><h3> [L-8] Lack of boolean return check on transfer or transferFrom </h3> 
There are some implementations of ERC20 tokens that do not revert if a transfer fails. Each transfer method has a boolean return that points to its success (or failure) and it is returned after a transfer is performed. If their return value is not checked a transfer may be failing silently having uncertain outcomes ranging from leaks of value to massive thefts. It is advisable wrapping each call to <code>transfer</code> and <code>transferFrom</code> with a <code>require</code> statement to enforce their truth.<br><br><em>Found 2 times</em>

```solidity
Governance.sol   L259:       VOTES.transferFrom(msg.sender, address(this), userVotes);
Governance.sol   L312:       VOTES.transferFrom(address(this), msg.sender, userVotes);
```
<br>


 <h2>Non-critical Risk Issues</h2> 
<h3> [N-1] No indexed parameters while emitting events </h3> 
Some events within the codebase lack from key indexed parameters. The usage of indexed parameters makes off-chain filtering possible. It is advised adding at least one relevant indexed parameter per emitted event.<br><br><em>Found 13 times</em>

```solidity
RANGE.sol   L20:       event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);
RANGE.sol   L21:       event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);
RANGE.sol   L22:       event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);
RANGE.sol   L23:       event CushionDown(bool high_, uint256 timestamp_);
RANGE.sol   L24:       event PricesChanged(
RANGE.sol   L30:       event SpreadsChanged(uint256 cushionSpread_, uint256 wallSpread_);
```
```solidity
Heart.sol   L29:       event RewardIssued(address to_, uint256 rewardAmount_);
Heart.sol   L30:       event RewardUpdated(ERC20 token_, uint256 rewardAmount_);
```
```solidity
Governance.sol   L86:       event ProposalSubmitted(uint256 proposalId);
Governance.sol   L87:       event ProposalEndorsed(uint256 proposalId, address voter, uint256 amount);
Governance.sol   L88:       event ProposalActivated(uint256 proposalId, uint256 timestamp);
Governance.sol   L89:       event WalletVoted(uint256 proposalId, address voter, bool for_, uint256 userVotes);
Governance.sol   L90:       event ProposalExecuted(uint256 proposalId);
```
<br><h3> [N-2] Public functions that are not called by the contract should be external </h3> 
If a function is not meant to be called inside a contract, it should be marked as external instead. According to the [Solidity Docs](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) an inherited virtual external function can be overridden and its behavior can be changed as public.<br><br><em>Found 10 times</em>

```solidity
RANGE.sol   L215:       function updateMarket(
```
```solidity
INSTR.sol   L28:       function VERSION() public pure override returns (uint8 major, uint8 minor) {
INSTR.sol   L37:       function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {
```
```solidity
MINTR.sol   L33:       function mintOhm(address to_, uint256 amount_) public permissioned {
MINTR.sol   L37:       function burnOhm(address from_, uint256 amount_) public permissioned {
```
```solidity
TRSRY.sol   L79:       function withdrawReserves(
```
```solidity
Kernel.sol   L439:       function grantRole(Role role_, address addr_) public onlyAdmin {
Kernel.sol   L451:       function revokeRole(Role role_, address addr_) public onlyAdmin {
```
```solidity
Governance.sol   L145:       function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {
Governance.sol   L151:       function getActiveProposal() public view returns (ActivatedProposal memory) {
```
<br><h3> [N-3] Constants should be declared instead of magic numbers </h3> 
While using constants across the codebase as comparing values, boundaries, among others; it is advisable to declare a self-explanatory constant instead of using numeric literals to provide more transparency.<br><br><em>Found 11 times</em>

```solidity
RANGE.sol   L245:       wallSpread_ > 10000 ||
RANGE.sol   L246:       wallSpread_ < 100 ||
RANGE.sol   L247:       cushionSpread_ > 10000 ||
RANGE.sol   L248:       cushionSpread_ < 100 ||
RANGE.sol   L264:       if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();
```
```solidity
Operator.sol   L518:       if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();
Operator.sol   L535:       if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();
Operator.sol   L550:       if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();
```
```solidity
Governance.sol   L164:       if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
Governance.sol   L217:       (totalEndorsementsForProposal[proposalId_] * 100) <
Governance.sol   L268:       if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {
```
<br><h3> [N-4] Repeated <code>require</code> statements or gate controls could be refactored to a modifier </h3> 
Whenever the same checks are needed to be performed many times across the codebase, it is advised to refactor them as modifiers or even functions in favour of readability and performance of the code.<br><br><em>Found 4 times</em>

```solidity
PRICE.sol   L124:       if (!initialized) revert Price_NotInitialized();
PRICE.sol   L155:       if (!initialized) revert Price_NotInitialized();
PRICE.sol   L184:       if (!initialized) revert Price_NotInitialized();
PRICE.sol   L191:       if (!initialized) revert Price_NotInitialized();
```
<br><h3> [N-5] Change token name on deployment </h3> 
The following lines contain a token deployment with a name that appears to be used only for testing. If this token will be deployed on production, review its name.<br><br><em>Found 1 time</em>

```solidity
VOTES.sol   L18:       ERC20("OlympusDAO Dummy Voting Tokens", "VOTES", 0)
```
<br><h3> [N-6] Function or public variable with missing or incomplete natspec </h3> 
Providing a complete, clear and understandable natspec on each function and public variable is a good practice that helps other devs and non-devs to understand quicker and better what is a function or variable intending to do/return. With functions, explain their intended behavior, returns and input parameters when applies. For public variables, what do they mean. The following instance(s) have incomplete or missing natspec.<br><br><em>Found 19 times</em>

```solidity
VOTES.sol   L35:       function mintTo(address wallet_, uint256 amount_) external permissioned {
VOTES.sol   L39:       function burnFrom(address wallet_, uint256 amount_) external permissioned {
VOTES.sol   L51:       function transferFrom(
```
```solidity
TRSRY.sol   L59:       function getReserveBalance(ERC20 token_) external view returns (uint256) {
TRSRY.sol   L64:       function setApprovalFor(
TRSRY.sol   L75:       function withdrawReserves(
TRSRY.sol   L92:       function getLoan(ERC20 token_, uint256 amount_) external permissioned {
TRSRY.sol   L105:       function repayLoan(ERC20 token_, uint256 amount_) external nonReentrant {
TRSRY.sol   L122:       function setDebt(
TRSRY.sol   L137:       function _checkApproval(
```
```solidity
TreasuryCustodian.sol   L27:       function configureDependencies() external override returns (Keycode[] memory dependencies) {
TreasuryCustodian.sol   L34:       function requestPermissions() external view override returns (Permissions[] memory requests) {
TreasuryCustodian.sol   L42:       function grantApproval(
TreasuryCustodian.sol   L53:       function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {
TreasuryCustodian.sol   L71:       function increaseDebt(
TreasuryCustodian.sol   L80:       function decreaseDebt(
```
```solidity
VoterRegistration.sol   L19:       function configureDependencies() external override returns (Keycode[] memory dependencies) {
VoterRegistration.sol   L27:       function requestPermissions()
```
```solidity
PriceConfig.sol   L18:       function configureDependencies() external override returns (Keycode[] memory dependencies) {
```
<br><h3> [N-7] Inconsistent way of declaring the pragma version </h3> 
The codebase mixes the ways <code>pragma</code> version is declared. Commonly codebases declare the pragma version with <code>^</code> or strictly. It is advised to unify the criteria regarding this issue.<br><br><em>Found 3 times</em>

```solidity
TRSRY.sol   L2:       pragma solidity 0.8.15;
```
```solidity
IHeart.sol   L2:       pragma solidity >=0.8.0;
```
```solidity
IOperator.sol   L2:       pragma solidity >=0.8.0;
```
<br><h3> [N-8] Capped variable names should be reserved for constant or immutable variables </h3> 
For the scenarios where the variable name should differ depending its origin, a pure or view function could be used instead to make this differentiation. Openzeppelin performs this strategy [here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)<br><br><em>Found 3 times</em>

```solidity
Heart.sol   L45:       OlympusPrice internal PRICE;
```
```solidity
Governance.sol   L56:       OlympusInstructions public INSTR;
Governance.sol   L57:       OlympusVotes public VOTES;
```
<br><h3> [N-9] Usage of <code>isContract</code> or similar </h3> 
It is an already known behavior that calls coming from the constructor while deploying a contract will appear to come as if they were coming from an EOA because the <code>codesize</code> is injected later on construction. It is advised to provide a well commented and addressed documentation regarding under which circumstances this function will be used (e.g. as an access control, function logic branching, etc.) and evaluate if the chance to circumvent this check with the ways mentioned before imply an issue for the protocol.<br><br><em>Found 1 time</em>

```solidity
KernelUtils.sol   L31:       function ensureContract(address target_) view {
```
<br>