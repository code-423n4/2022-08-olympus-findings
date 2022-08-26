### [G-01] Using bools for storage incurs overhead.


#### Impact
 
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use ```uint256(1)``` and ```uint256(2)``` for true/false to avoid a Gwarmaccess ([100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past


#### Findings:
```
src/modules/PRICE.sol:L62    bool public initialized;
src/policies/Heart.sol:L33    bool public active;

src/policies/Operator.sol:L63    bool public initialized;

src/policies/Operator.sol:L66    bool public active;

src/policies/BondCallback.sol:L24    mapping(address => mapping(uint256 => bool)) public approvedMarkets;

src/policies/Governance.sol:L105    mapping(uint256 => bool) public proposalHasBeenActivated;

src/policies/Governance.sol:L117    mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;
```

###  [G-02] Cache Array Length Outside of Loop


#### Impact
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.


#### Findings:
```
src/policies/Governance.sol:L278        for (uint256 step; step < instructions.length; ) {
```

### [G-03] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
src/modules/VOTES.sol:L19    {}

src/modules/TRSRY.sol:L45    constructor(Kernel kernel_) Module(kernel_) {}

src/modules/INSTR.sol:L20    constructor(Kernel kernel_) Module(kernel_) {}

src/policies/PriceConfig.sol:L15    constructor(Kernel kernel_) Policy(kernel_) {}

src/policies/VoterRegistration.sol:L16    constructor(Kernel kernel_) Policy(kernel_) {}

src/policies/TreasuryCustodian.sol:L24    constructor(Kernel kernel_) Policy(kernel_) {}

src/policies/Governance.sol:L59    constructor(Kernel kernel_) Policy(kernel_) {}

```
### [G-04] Use a more recent version of solidity.


#### Impact
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


#### Findings:
```
src/interfaces/IBondCallback.sol:L2      pragma solidity >=0.8.0;

src/policies/interfaces/IOperator.sol:L2      pragma solidity >=0.8.0;

src/policies/interfaces/IHeart.sol:L2      pragma solidity >=0.8.0;
```

### [G-05] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
src/policies/Heart.sol:L130    function resetBeat() external onlyRole("heart_admin") {

src/policies/Heart.sol:L135    function toggleBeat() external onlyRole("heart_admin") {

src/policies/Heart.sol:L150    function withdrawUnspentRewards(ERC20 token_) external onlyRole("heart_admin") {

src/policies/VoterRegistration.sol:L45    function issueVotesTo(address wallet_, uint256 amount_) external onlyRole("voter_admin") {

src/policies/VoterRegistration.sol:L53    function revokeVotesFrom(address wallet_, uint256 amount_) external onlyRole("voter_admin") {

src/policies/Operator.sol:L195    function operate() external override onlyWhileActive onlyRole("operator_operate") {

src/policies/Operator.sol:L510    function setThresholdFactor(uint256 thresholdFactor_) external onlyRole("operator_policy") {

src/policies/Operator.sol:L516    function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {

src/policies/Operator.sol:L548    function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {

src/policies/Operator.sol:L598    function initialize() external onlyRole("operator_admin") {

src/policies/Operator.sol:L618    function regenerate(bool high_) external onlyRole("operator_admin") {

src/policies/Operator.sol:L624    function toggleActive() external onlyRole("operator_admin") {

src/policies/BondCallback.sol:L152    function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {

src/policies/BondCallback.sol:L190    function setOperator(Operator operator_) external onlyRole("callback_admin") {

```

### [G-06] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
src/modules/VOTES.sol:L58            balanceOf[to_] += amount_;

src/modules/PRICE.sol:L136            _movingAverage += (currentPrice - earliestPrice) / numObs;

src/modules/PRICE.sol:L222            total += startObservations_[i];

src/modules/TRSRY.sol:L96        reserveDebt[token_][msg.sender] += amount_;

src/modules/TRSRY.sol:L97        totalDebt[token_] += amount_;

src/modules/TRSRY.sol:L131        if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;

src/policies/Heart.sol:L103        lastBeat += frequency();

src/policies/BondCallback.sol:L143        _amountsPerMarket[id_][0] += inputAmount_;

src/policies/BondCallback.sol:L144        _amountsPerMarket[id_][1] += outputAmount_;

src/policies/Governance.sol:L198        totalEndorsementsForProposal[proposalId_] += userVotes;

src/policies/Governance.sol:L252            yesVotesForProposal[activeProposal.proposalId] += userVotes;

src/policies/Governance.sol:L254            noVotesForProposal[activeProposal.proposalId] += userVotes;

```
### [G-07] ++i costs less gas than i++, especially when it's used in for loops.


#### Impact
Saves 6 gas per instance.


#### Findings:
```
src/policies/Operator.sol:L488            decimals++;

src/policies/Operator.sol:L670                _status.low.count++;

src/policies/Operator.sol:L686                _status.high.count++;

src/utils/KernelUtils.sol:L49            i++;

src/utils/KernelUtils.sol:L64            i++;
```

### [G-08] Using private rather than public for constants to saves gas.


#### Impact
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.


#### Findings:
```
src/modules/PRICE.sol:L59    uint8 public constant decimals = 18;

src/modules/RANGE.sol:L65    uint256 public constant FACTOR_SCALE = 1e4;

src/policies/Operator.sol:L89    uint32 public constant FACTOR_SCALE = 1e4;

src/policies/Governance.sol:L121    uint256 public constant SUBMISSION_REQUIREMENT = 100;

src/policies/Governance.sol:L124    uint256 public constant ACTIVATION_DEADLINE = 2 weeks;

src/policies/Governance.sol:L127    uint256 public constant GRACE_PERIOD = 1 weeks;

src/policies/Governance.sol:L130    uint256 public constant ENDORSEMENT_THRESHOLD = 20;

src/policies/Governance.sol:L133    uint256 public constant EXECUTION_THRESHOLD = 33;

src/policies/Governance.sol:L137    uint256 public constant EXECUTION_TIMELOCK = 3 days;
```

### [G-09] Explicit initialization with zero not required


#### Impact
Explicit initialization with zero is not required for variable declaration because uints are 0 by default. Removing this will reduce contract size and save a bit of gas.


#### Findings:
```
src/utils/KernelUtils.sol:L43    for (uint256 i = 0; i < 5; ) {

src/utils/KernelUtils.sol:L58    for (uint256 i = 0; i < 32; ) {
```

