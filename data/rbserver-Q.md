## [L-01] IT IS POSSIBLE THAT beat FUNCTION IS NOT CALLED FOR ENTIRE TIME DURING A FREQUENCY
It is possible that the following `beat` function is not called by anybody for the entire time during a frequency. In this case, `PRICE.updateMovingAverage()` is not executed for that frequency. The price information for that frequency is not recorded, and the moving average becomes out-of-date as it is not updated with that frequency's price. Later, after someone calls `beat` again during a new frequency, the price information for the skipped frequency is still missing, and the duration between the current and earliest observations will be larger than specified. Because of this, the moving average deviates from the time-weighted average price to be more like an observation-weighted average price, which is also not as specified. To avoid these bookkeeping discrepancies, it can be beneficial to set up a bot to call `beat` for once during each frequency just in case nobody calls it during a frequency.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L92-L109
```solidity
    function beat() external nonReentrant {
        if (!active) revert Heart_BeatStopped();
        if (block.timestamp < lastBeat + frequency()) revert Heart_OutOfCycle();

        // Update the moving average on the Price module
        PRICE.updateMovingAverage();

        // Trigger price range update and market operations
        _operator.operate();

        // Update the last beat timestamp
        lastBeat += frequency();

        // Issue reward to sender
        _issueReward(msg.sender);

        emit Beat(block.timestamp);
    }
```

## [L-02] UNRESOLVED TODO COMMENTS
Comment regarding todo indicates that there is an unresolved action item for implementation, which need to be addressed before protocol deployment. Please review the todo comments in the following code.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L657
```solidity
        /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
```

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L51-L67
```solidity
    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
    // TODO must reorg policy storage to be able to check for deactivated policies.
    function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {
        if (Policy(policy_).isActive()) revert PolicyStillActive();

        // TODO Make sure `policy_` is an actual policy and not a random address.

        uint256 len = tokens_.length;
        for (uint256 j; j < len; ) {
            TRSRY.setApprovalFor(policy_, tokens_[j], 0);
            unchecked {
                ++j;
            }
        }

        emit ApprovalRevoked(policy_, tokens_);
    }
```

## [L-03] MISSING ZERO-ADDRESS CHECK FOR CRITICAL ADDRESSES
To prevent unintended behaviors, the critical address inputs should be checked against `address(0)`.

Please consider checking `ohm_` in the following constructor.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L15-L17
```solidity
    constructor(Kernel kernel_, address ohm_) Module(kernel_) {
        ohm = OHM(ohm_);
    }
```

Please consider checking the addresses of `ohmEthPriceFeed_` and `reserveEthPriceFeed_` in the following constructor.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L71-L77
```solidity
    constructor(
        Kernel kernel_,
        AggregatorV2V3Interface ohmEthPriceFeed_,
        AggregatorV2V3Interface reserveEthPriceFeed_,
        uint48 observationFrequency_,
        uint48 movingAverageDuration_
    ) Module(kernel_) {
```

Please consider checking the addresses in `tokens_` in the following constructor.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L77-L81
```solidity
    constructor(
        Kernel kernel_,
        ERC20[2] memory tokens_,
        uint256[3] memory rangeParams_ // [thresholdFactor, cushionSpread, wallSpread]
    ) Module(kernel_) {
```

Please consider checking the addresses of `aggregator_` and `ohm_` in the following constructor.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L38-L45
```solidity
    constructor(
        Kernel kernel_,
        IBondAggregator aggregator_,
        ERC20 ohm_
    ) Policy(kernel_) {
        aggregator = aggregator_;
        ohm = ohm_;
    }
```

Please consider checking the addresses in `tokens_` in the following constructor.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L92-L98
```solidity
    constructor(
        Kernel kernel_,
        IBondAuctioneer auctioneer_,
        IBondCallback callback_,
        ERC20[2] memory tokens_, // [ohm, reserve]
        uint32[8] memory configParams // [cushionFactor, cushionDuration, cushionDebtBuffer, cushionDepositInterval, reserveFactor, regenWait, regenThreshold, regenObserve]
    ) Policy(kernel_) {
```

Please consider checking the address of `rewardToken_` in the following constructor.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L54-L59
```solidity
    constructor(
        Kernel kernel_,
        IOperator operator_,
        ERC20 rewardToken_,
        uint256 reward_
    ) Policy(kernel_) {
```

## [N-01] ESUBMISSION_REQUIREMENT IS USED TO COMPARE AGAINST 10000 BUT ENDORSEMENT_THRESHOLD AND EXECUTION_THRESHOLD ARE USED TO COMPARE AGAINST 100
`ESUBMISSION_REQUIREMENT`, `ENDORSEMENT_THRESHOLD`, and  `EXECUTION_THRESHOLD` in the following code are all used to represent percents. However, `ESUBMISSION_REQUIREMENT` is used to compare against 10000 while `ENDORSEMENT_THRESHOLD` and `EXECUTION_THRESHOLD` are used to compare against 100. This inconsistency can cause confusions and typos in the future. Please consider unifying these constants so they can be used to compare against the same number.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L119-L133
```solidity
    /// @notice The amount of votes a proposer needs in order to submit a proposal as a percentage of total supply (in basis points).
    /// @dev    This is set to 1% of the total supply.
    uint256 public constant SUBMISSION_REQUIREMENT = 100;

    ...

    /// @notice Endorsements required to activate a proposal as percentage of total supply.
    uint256 public constant ENDORSEMENT_THRESHOLD = 20;

    /// @notice Net votes required to execute a proposal on chain as a percentage of total supply.
    uint256 public constant EXECUTION_THRESHOLD = 33;

```

## [N-02] Unreachable code
`return true;` is unreachable in the following code. It can be removed for better readability and maintainability.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L45-L48
```solidity
    function transfer(address to_, uint256 amount_) public pure override returns (bool) {
        revert VOTES_TransferDisabled();
        return true;
    }
```

## [N-03] decimals CAN BE NAMED USING CAPITAL LETTERS AND UNDERSCORES
Because the following `decimals` is a constant, it can be named using capital letters and underscores by convention, which improves readability and maintainability.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59
```solidity
    uint8 public constant decimals = 18;
```

## [N-04] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, constants can be used instead of the magic numbers. Please consider replacing the magic numbers used in the following code with constants.
```solidity
modules\PRICE.sol
  90: if (exponent > 38) revert Price_InvalidParams();   

modules\RANGE.sol
  245: wallSpread_ > 10000 || 
  246: wallSpread_ < 100 || 
  247: cushionSpread_ > 10000 || 
  248: cushionSpread_ < 100 || 

policies\Governance.sol
  164: if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
  217: (totalEndorsementsForProposal[proposalId_] * 100) <

policies\Operator.sol
  103: if (configParams[1] > uint256(7 days) || configParams[1] < uint256(1 days))
  106: if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();
  108: if (configParams[3] < uint32(1 hours) || configParams[3] > configParams[1])
  111: if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();
  114: configParams[5] < 1 hours ||
  378: 36 + scaleAdjustment + int8(reserveDecimals) - int8(ohmDecimals) - priceDecimals   
  433: 36 + scaleAdjustment + int8(ohmDecimals) - int8(reserveDecimals) - priceDecimals   
  533: if (duration_ > uint256(7 days) || duration_ < uint256(1 days))
  535: if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();
  536: if (depositInterval_ < uint32(1 hours) || depositInterval_ > duration_)
  550: if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();
  565: if (wait_ < 1 hours || threshold_ > observe_ || observe_ == 0)
```

## [N-05] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. @param or @return comments are missing for the following functions. Please consider completing NatSpec comments for them.
```solidity
Kernel.sol
  235: function executeAction(Actions action_, address target_) external onlyExec
  351: function _migrateKernel(Kernel newKernel_) internal   
  439: function grantRole(Role role_, address addr_) public onlyAdmin {  
  451: function revokeRole(Role role_, address addr_) public onlyAdmin {

modules\TRSRY.sol
  64: function setApprovalFor(  
  75: function withdrawReserves(  
  92: function getLoan(ERC20 token_, uint256 amount_) external permissioned {  
  105: function repayLoan(ERC20 token_, uint256 amount_) external nonReentrant {  
  122: function setDebt(  

modules\VOTES.sol
  45: function transfer(address to_, uint256 amount_) public pure override returns (bool) {  
  51: function transferFrom(  
```

## [N-06] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.
```solidity
Kernel.sol
  266: function _installModule(Module newModule_) internal {  
  279: function _upgradeModule(Module newModule_) internal {  
  295: function _activatePolicy(Policy policy_) internal {  
  325: function _deactivatePolicy(Policy policy_) internal {  
  378: function _reconfigurePolicies(Keycode keycode_) internal { 
  391: function _setPolicyPermissions( 
  409: function _pruneFromDependents(Policy policy_) internal { 

modules\MINTR.sol
  33: function mintOhm(address to_, uint256 amount_) public permissioned {   
  37: function burnOhm(address from_, uint256 amount_) public permissioned {   

modules\TRSRY.sol
  47: function KEYCODE() public pure override returns (Keycode) {  
  51: function VERSION() external pure override returns (uint8 major, uint8 minor) {  
  59: function getReserveBalance(ERC20 token_) external view returns (uint256) {  
  137: function _checkApproval(  

modules\VOTES.sol
  35: function mintTo(address wallet_, uint256 amount_) external permissioned {  
  39: function burnFrom(address wallet_, uint256 amount_) external permissioned {  

utils\KernelUtils.sol
  11: function toKeycode(bytes5 keycode_) pure returns (Keycode) {   
  16: function fromKeycode(Keycode keycode_) pure returns (bytes5) {  
  21: function toRole(bytes32 role_) pure returns (Role) {  
  26: function fromRole(Role role_) pure returns (bytes32) {  
  31: function ensureContract(address target_) view {  
  40: function ensureValidKeycode(Keycode keycode_) pure {  
  55: function ensureValidRole(Role role_) pure {  
```