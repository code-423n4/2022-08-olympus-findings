# [G-01] **tate variables only set in the constructor should be declared `immutable`** (Avoids a Gsset (20000 gas)):



1. File: 2022-08-olympus/src/Kernel.sol (line 155-158):

   `    address public executor;

    /// @notice Address that is responsible for assigning policy-defined roles to addresses.
    address public admin;`

2. File: 2022-08-olympus/src/Kernel.sol (line 188):

   `Policy[] public activePolicies;`

3. File: 2022-08-olympus/src/modules/PRICE.sol (line 41-56):

   `    uint256[] public observations;

    /// @notice Index of the next observation to make. The current value at this index is the oldest observation.
    uint32 public nextObsIndex;

    /// @notice Number of observations used in the moving average calculation. Computed from movingAverageDuration / observationFrequency.
    uint32 public numObservations;

    /// @notice Frequency (in seconds) that observations should be stored.
    uint48 public observationFrequency;

    /// @notice Duration (in seconds) over which the moving average is calculated.
    uint48 public movingAverageDuration;

    /// @notice Unix timestamp of last observation (in seconds).
    uint48 public lastObservationTime;`

4. File: 2022-08-olympus/src/modules/INSTR.sol (line 13):

   `uint256 public totalInstructions;`       

5. File: 2022-08-olympus/src/policies/Operator.sol (line 76-78):

   `   IBondAuctioneer public auctioneer;
    /// @notice     Callback contract used for cushion bond market payouts
    IBondCallback public callback;`

6. File: 2022-08-olympus/src/policies/BondCallback.sol (line 28-32):

   `    IBondAggregator public aggregator;
    OlympusTreasury public TRSRY;
    OlympusMinter public MINTR;
    Operator public operator;
    ERC20 public ohm;`

7. File: 2022-08-olympus/src/policies/Heart.sol (line 36-42):

   `    uint256 public lastBeat;

    /// @notice Reward for beating the Heart (in reward token decimals)
    uint256 public reward;

    /// @notice Reward token address that users are sent for beating the Heart
    ERC20 public rewardToken;`    

8. File: 2022-08-olympus/src/policies/VoterRegistration.sol (line 10):

   `OlympusVotes public VOTES;`









# [G-02] `x = x + y` is cheaper than `x += y`:



1. File: 2022-08-olympus/src/modules/TRSRY.sol (line 115-116):

   `        reserveDebt[token_][msg.sender] -= received;
        totalDebt[token_] -= received;`

2. File: 2022-08-olympus/src/modules/TRSRY.sol (line 132):

   `else totalDebt[token_] -= oldDebt - amount_;`

3. File: 2022-08-olympus/src/modules/PRICE.sol (line 138):

   `_movingAverage -= (earliestPrice - currentPrice) / numObs;`

4. File: 2022-08-olympus/src/modules/VOTES.sol (line 56):

   `balanceOf[from_] -= amount_;`       

5. File: 2022-08-olympus/src/policies/Governance.sol (line 194):

   `totalEndorsementsForProposal[proposalId_] -= previousEndorsement;`

6. File: 2022-08-olympus/src/policies/BondCallback.sol (line 28-32):

   `    IBondAggregator public aggregator;
    OlympusTreasury public TRSRY;
    OlympusMinter public MINTR;
    Operator public operator;
    ERC20 public ohm;`

7. File: 2022-08-olympus/src/modules/TRSRY.sol (line 96-97):

   `        reserveDebt[token_][msg.sender] += amount_;
        totalDebt[token_] += amount_;`

8. File: 2022-08-olympus/src/modules/TRSRY.sol (line 131):

   `if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;` 

9. File: 2022-08-olympus/src/modules/PRICE.sol (line 136):

   `_movingAverage += (currentPrice - earliestPrice) / numObs;`

10. File: 2022-08-olympus/src/modules/PRICE.sol (line 222):

   `total += startObservations_[i];` 

11. File: 2022-08-olympus/src/modules/VOTES.sol (line 58):

   `balanceOf[to_] += amount_;`

12. File: 2022-08-olympus/src/policies/BondCallback.sol (line 143-144):

   `        _amountsPerMarket[id_][0] += inputAmount_;
        _amountsPerMarket[id_][1] += outputAmount_;`

13. File: 2022-08-olympus/src/policies/Heart.sol (line 103):

   `lastBeat += frequency();` 

14. File: 2022-08-olympus/src/policies/Governance.sol (line 198):

   `totalEndorsementsForProposal[proposalId_] += userVotes;`

15. File: 2022-08-olympus/src/policies/Governance.sol 252-254):

   `            yesVotesForProposal[activeProposal.proposalId] += userVotes;
        } else {
            noVotesForProposal[activeProposal.proposalId] += userVotes;`                        
           


        
# [G-03] `<array>.length` should not be looked up in every loop of a `for` loop:



1. File: 2022-08-olympus/src/policies/Governance.sol (line 278):

   `for (uint256 step; step < instructions.length; ) {`
      

    

# [G-04] Not using the named return variables when a function returns, wastes deployment gas:



1. File: 2022-08-olympus/src/modules/RANGE.sol (line 276):

   `return _range;`

2. File: 2022-08-olympus/src/policies/Operator.sol (line 784):

   `return _status;`

3. File: 2022-08-olympus/src/policies/Operator.sol (line 799):

   ` return _config;`








# [G-05] It costs more gas to initialize variables to zero than to let the default of zero be applied:



1. File: 2022-08-olympus/src/Kernel.sol (line 276):

   `for (uint256 i = 0; i < reqLength; ) {`

2. File: 2022-08-olympus/src/utils/KernelUtils.sol (line 58):

   `for (uint256 i = 0; i < 32; ) {`

3. File: 2022-08-olympus/src/utils/KernelUtils.sol (line 43):

   `for (uint256 i = 0; i < 5; ) {`

4. File: 2022-08-olympus/src/modules/PRICE.sol (line 253-255):

   `        lastObservationTime = 0;
        _movingAverage = 0;
        nextObsIndex = 0;`       

5. File: 2022-08-olympus/src/modules/PRICE.sol (line 285-287):

   `        lastObservationTime = 0;
        _movingAverage = 0;
        nextObsIndex = 0;`

6. File: 2022-08-olympus/src/policies/Operator.sol(line 574-575):

   `        _status.high.count = 0;
        _status.high.nextObservation = 0;` 

7. File: 2022-08-olympus/src/policies/Operator.sol (line 578-579):

   `        _status.low.count = 0;
        _status.low.nextObservation = 0;`



    


# [G-06] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead (> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed):



1. File: 2022-08-olympus/src/Kernel.sol (line 100):

   `function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}`

2. File: 2022-08-olympus/src/modules/TRSRY.sol (line 51):

   `function VERSION() external pure override returns (uint8 major, uint8 minor) {`

3. File: 2022-08-olympus/src/modules/MINTR.sol (line 25):

   `function VERSION() external pure override returns (uint8 major, uint8 minor) {`

4. File: 2022-08-olympus/src/modules/RANGE.sol (line 45):

   `uint48 lastActive;`       

5. File: 2022-08-olympus/src/modules/RANGE.sol (line 115):

   `function VERSION() external pure override returns (uint8 major, uint8 minor) {`

6. File: 2022-08-olympus/src/modules/RANGE.sol(line 136):

   `_range.high.lastActive = uint48(block.timestamp);` 

7. File: 2022-08-olympus/src/modules/RANGE.sol (line 148):

   `_range.low.lastActive = uint48(block.timestamp);`    

8. File: 2022-08-olympus/src/modules/PRICE.sol (line 27-28):

   `    event MovingAverageDurationChanged(uint48 movingAverageDuration_);
    event ObservationFrequencyChanged(uint48 observationFrequency_);`       

9. File: 2022-08-olympus/src/modules/PRICE.sol (line 44-59):

   ` uint32 public nextObsIndex;

    /// @notice Number of observations used in the moving average calculation. Computed from movingAverageDuration / observationFrequency.
    uint32 public numObservations;

    /// @notice Frequency (in seconds) that observations should be stored.
    uint48 public observationFrequency;

    /// @notice Duration (in seconds) over which the moving average is calculated.
    uint48 public movingAverageDuration;

    /// @notice Unix timestamp of last observation (in seconds).
    uint48 public lastObservationTime;

    /// @notice Number of decimals in the price values provided by the contract.
    uint8 public constant decimals = 18;`

10. File: 2022-08-olympus/src/policies/Operator.sol(line 75-76):

   `        uint48 observationFrequency_,
        uint48 movingAverageDuration_` 

11. File: 2022-08-olympus/src/policies/Operator.sol (line 84):

   `uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();`

12. File: 2022-08-olympus/src/modules/PRICE.sol (line 87):

   `uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();`

13. File: 2022-08-olympus/src/modules/PRICE.sol (line 97):

   `numObservations = uint32(movingAverageDuration_ / observationFrequency_);`

14. File: 2022-08-olympus/src/modules/PRICE.sol (line 113):

   `function VERSION() external pure override returns (uint8 major, uint8 minor) {` 

15. File: 2022-08-olympus/src/modules/PRICE.sol (line 127):

   `uint32 numObs = numObservations;`

16. File: 2022-08-olympus/src/modules/PRICE.sol (line 143):

   `lastObservationTime = uint48(block.timestamp);`  

17. File: 2022-08-olympus/src/modules/PRICE.sol (line 185):

   `uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;`

18. File: 2022-08-olympus/src/modules/PRICE.sol (line 215):

   `if (startObservations_.length != numObs || lastObservationTime_ > uint48(block.timestamp))`

19. File: 2022-08-olympus/src/modules/PRICE.sol (line 240):

   `function changeMovingAverageDuration(uint48 movingAverageDuration_) external permissioned {`

20. File: 2022-08-olympus/src/modules/PRICE.sol (line 257):

   `numObservations = uint32(newObservations);`

21. File: 2022-08-olympus/src/modules/PRICE.sol (line 266):

   `function changeObservationFrequency(uint48 observationFrequency_) external permissioned {`

22. File: 2022-08-olympus/src/modules/PRICE.sol (line 289):

   `numObservations = uint32(newObservations);`

23. 2022-08-olympus/src/modules/VOTES.sol (line 27):

   `function VERSION() external pure override returns (uint8 major, uint8 minor) {`

24. 2022-08-olympus/src/modules/INSTR.sol (line 28):

   `function VERSION() public pure override returns (uint8 major, uint8 minor) {`

25. 2022-08-olympus/src/policies/Operator.sol (line 51-54):

   `    event CushionFactorChanged(uint32 cushionFactor_);
    event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);
    event ReserveFactorChanged(uint32 reserveFactor_);
    event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);`

26. 2022-08-olympus/src/policies/Operator.sol (line 83):

   `uint8 public immutable ohmDecimals;`

27. 2022-08-olympus/src/policies/Operator.sol (line 86-89):

   `    uint8 public immutable reserveDecimals;

    /// Constants
    uint32 public constant FACTOR_SCALE = 1e4;`

28. 2022-08-olympus/src/policies/Operator.sol (line 51-54):

   `uint32[8] memory configParams`

29. 2022-08-olympus/src/policies/Operator.sol (line 106-108):

   `        if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();

        if (configParams[3] < uint32(1 hours) || configParams[3] > configParams[1])`

30. 2022-08-olympus/src/policies/Operator.sol (line 116):

   `configParams[7] == uint32(0)`

31. 2022-08-olympus/src/policies/Operator.sol (line 127-129):

   `            count: uint32(0),
            lastRegen: uint48(block.timestamp),
            nextObservation: uint32(0),`

32. 2022-08-olympus/src/policies/Operator.sol (line 210):

   `uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&`

33. 2022-08-olympus/src/policies/Operator.sol (line 216):

   `uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&`

34. 2022-08-olympus/src/policies/Operator.sol (line 403-404):

   `                vesting: uint48(0), // Instant swaps
                conclusion: uint48(block.timestamp + config_.cushionDuration),`

35. 2022-08-olympus/src/policies/Operator.sol (line 418):

   `uint8 oracleDecimals = PRICE.decimals();`

36. 2022-08-olympus/src/policies/Operator.sol (line 455-456):

   `                vesting: uint48(0), // Instant swaps
                conclusion: uint48(block.timestamp + config_.cushionDuration),` 

37. 2022-08-olympus/src/policies/Operator.sol (line 516):

   `function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {`

38. 2022-08-olympus/src/policies/Operator.sol (line 528-530):

   `        uint32 duration_,
        uint32 debtBuffer_,
        uint32 depositInterval_` 

39. 2022-08-olympus/src/policies/Operator.sol (line 535-536):

   `        if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();
        if (depositInterval_ < uint32(1 hours) || depositInterval_ > duration_)`

40. 2022-08-olympus/src/policies/Operator.sol (line 548):

   `function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {`

41. 2022-08-olympus/src/policies/Operator.sol (line 560-562):

   `        uint32 wait_,
        uint32 threshold_,
        uint32 observe_`

42. 2022-08-olympus/src/policies/Operator.sol (line 665):

   `uint32 observe = _config.regenObserve;`

43. 2022-08-olympus/src/policies/Operator.sol (line 560-562):

   `        uint32 wait_,
        uint32 threshold_,
        uint32 observe_`

44. 2022-08-olympus/src/policies/Operator.sol (line 705):

   `_status.high.count = uint32(0);` 

45. 2022-08-olympus/src/policies/Operator.sol (line 707-708):

   `            _status.high.nextObservation = uint32(0);
            _status.high.lastRegen = uint48(block.timestamp);`

46. 2022-08-olympus/src/policies/Operator.sol (line 717):

   `_status.low.count = uint32(0);` 

47. 2022-08-olympus/src/policies/Operator.sol (line 719-720):

   `            _status.low.nextObservation = uint32(0);
            _status.low.lastRegen = uint48(block.timestamp);` 

48. 2022-08-olympus/src/policies/PriceConfig.sol (line 58):

   `function changeMovingAverageDuration(uint48 movingAverageDuration_)` 

49. 2022-08-olympus/src/policies/PriceConfig.sol (line 69):

   `function changeObservationFrequency(uint48 observationFrequency_)`

50. 2022-08-olympus/src/policies/interfaces/IOperator.sol (line 13-20):

   `        uint32 cushionFactor; // percent of capacity to be used for a single cushion deployment, assumes 2 decimals (i.e. 1000 = 10%)
        uint32 cushionDuration; // duration of a single cushion deployment in seconds
        uint32 cushionDebtBuffer; // Percentage over the initial debt to allow the market to accumulate at any one time. Percent with 3 decimals, e.g. 1_000 = 1 %. See IBondAuctioneer for more info.
        uint32 cushionDepositInterval; // Target frequency of deposits. Determines max payout of the bond market. See IBondAuctioneer for more info.
        uint32 reserveFactor; // percent of reserves in treasury to be used for a single wall, assumes 2 decimals (i.e. 1000 = 10%)
        uint32 regenWait; // minimum duration to wait to reinstate a wall in seconds
        uint32 regenThreshold; // number of price points on other side of moving average to reinstate a wall
        uint32 regenObserve; // number of price points to observe to determine regeneration` 

51. 2022-08-olympus/src/policies/interfaces/IOperator.sol (line 31-33):

   `        uint32 count; // current number of price points that count towards regeneration
        uint48 lastRegen; // timestamp of the last regeneration
        uint32 nextObservation; // index of the next observation in the observations array`

52. 2022-08-olympus/src/policies/interfaces/IOperator.sol (line 93-95):

   `       uint32 duration_,
        uint32 debtBuffer_,
        uint32 depositInterval_` 

53. 2022-08-olympus/src/policies/interfaces/IOperator.sol (line 101):

   `function setReserveFactor(uint32 reserveFactor_) external;` 

54. 2022-08-olympus/src/policies/interfaces/IOperator.sol (line 110-112):

   `       uint32 wait_,
        uint32 threshold_,
        uint32 observe_` 

                                                                                                                                     





# [G-06] Functions guaranteed to revert when called by normal users can be marked `payable` (If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.):



1. File: 2022-08-olympus/src/policies/TreasuryCustodian.sol (line 42-46):

   `   function grantApproval(
        address for_,
        ERC20 token_,
        uint256 amount_
    ) external onlyRole("custodian") {`

2. File: 2022-08-olympus/src/policies/TreasuryCustodian.sol (line 71-75):

   `    function increaseDebt(
        ERC20 token_,
        address debtor_,
        uint256 amount_
    ) external onlyRole("custodian") {`

3. File: 2022-08-olympus/src/policies/TreasuryCustodian.sol (line 80-85):

   `    function decreaseDebt(
        ERC20 token_,
        address debtor_,
        uint256 amount_
    ) external onlyRole("custodian") {`

4. File: 2022-08-olympus/src/policies/Operator.sol (line 195):

   `function operate() external override onlyWhileActive onlyRole("operator_operate") {`       

5. File: 2022-08-olympus/src/policies/Operator.sol (line 346-350):

   `function bondPurchase(uint256 id_, uint256 amountOut_)
        external
        onlyWhileActive
        onlyRole("operator_reporter")
    {`

6. File: 2022-08-olympus/src/policies/Operator.sol(line 498-624):

   `     function setSpreads(uint256 cushionSpread_, uint256 wallSpread_)
        external
        onlyRole("operator_policy")
    {
        /// Set spreads on the range module
        RANGE.setSpreads(cushionSpread_, wallSpread_);

        /// Update range prices (wall and cushion)
        _updateRangePrices();
    }

    /// @inheritdoc IOperator
    function setThresholdFactor(uint256 thresholdFactor_) external onlyRole("operator_policy") {
        /// Set threshold factor on the range module
        RANGE.setThresholdFactor(thresholdFactor_);
    }

    /// @inheritdoc IOperator
    function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {
        /// Confirm factor is within allowed values
        if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();

        /// Set factor
        _config.cushionFactor = cushionFactor_;

        emit CushionFactorChanged(cushionFactor_);
    }

    /// @inheritdoc IOperator
    function setCushionParams(
        uint32 duration_,
        uint32 debtBuffer_,
        uint32 depositInterval_
    ) external onlyRole("operator_policy") {
        /// Confirm values are valid
        if (duration_ > uint256(7 days) || duration_ < uint256(1 days))
            revert Operator_InvalidParams();
        if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();
        if (depositInterval_ < uint32(1 hours) || depositInterval_ > duration_)
            revert Operator_InvalidParams();

        /// Update values
        _config.cushionDuration = duration_;
        _config.cushionDebtBuffer = debtBuffer_;
        _config.cushionDepositInterval = depositInterval_;

        emit CushionParamsChanged(duration_, debtBuffer_, depositInterval_);
    }

    /// @inheritdoc IOperator
    function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {
        /// Confirm factor is within allowed values
        if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();

        /// Set factor
        _config.reserveFactor = reserveFactor_;

        emit ReserveFactorChanged(reserveFactor_);
    }

    /// @inheritdoc IOperator
    function setRegenParams(
        uint32 wait_,
        uint32 threshold_,
        uint32 observe_
    ) external onlyRole("operator_policy") {
        /// Confirm regen parameters are within allowed values
        if (wait_ < 1 hours || threshold_ > observe_ || observe_ == 0)
            revert Operator_InvalidParams();

        /// Set regen params
        _config.regenWait = wait_;
        _config.regenThreshold = threshold_;
        _config.regenObserve = observe_;

        /// Re-initialize regen structs with new values (except for last regen)
        _status.high.count = 0;
        _status.high.nextObservation = 0;
        _status.high.observations = new bool[](observe_);

        _status.low.count = 0;
        _status.low.nextObservation = 0;
        _status.low.observations = new bool[](observe_);

        emit RegenParamsChanged(wait_, threshold_, observe_);
    }

    /// @inheritdoc IOperator
    function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_)
        external
        onlyRole("operator_admin")
    {
        if (address(auctioneer_) == address(0) || address(callback_) == address(0))
            revert Operator_InvalidParams();
        /// Set contracts
        auctioneer = auctioneer_;
        callback = callback_;
    }

    /// @inheritdoc IOperator
    function initialize() external onlyRole("operator_admin") {
        /// Can only call once
        if (initialized) revert Operator_AlreadyInitialized();

        /// Request approval for reserves from TRSRY
        TRSRY.setApprovalFor(address(this), reserve, type(uint256).max);

        /// Update range prices (wall and cushion)
        _updateRangePrices();

        /// Regenerate sides
        _regenerate(true);
        _regenerate(false);

        /// Set initialized and active flags
        initialized = true;
        active = true;
    }

    /// @inheritdoc IOperator
    function regenerate(bool high_) external onlyRole("operator_admin") {
        /// Regenerate side
        _regenerate(high_);
    }

    /// @inheritdoc IOperator
    function toggleActive() external onlyRole("operator_admin") {` 

7. File: 2022-08-olympus/src/policies/BondCallback.sol (line 152):

   ` function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {`    

8. File: 2022-08-olympus/src/policies/BondCallback.sol (line 190):

   `function setOperator(Operator operator_) external onlyRole("callback_admin") {`       

9. File: 2022-08-olympus/src/policies/Heart.sol (line 130-152):

   ` function resetBeat() external onlyRole("heart_admin") {
        lastBeat = block.timestamp - frequency();
    }

    /// @inheritdoc IHeart
    function toggleBeat() external onlyRole("heart_admin") {
        active = !active;
    }

    /// @inheritdoc IHeart
    function setRewardTokenAndAmount(ERC20 token_, uint256 reward_)
        external
        onlyRole("heart_admin")
    {
        rewardToken = token_;
        reward = reward_;
        emit RewardUpdated(token_, reward_);
    }

    /// @inheritdoc IHeart
    function withdrawUnspentRewards(ERC20 token_) external onlyRole("heart_admin") {
        token_.safeTransfer(msg.sender, token_.balanceOf(address(this)));
    }`

10. File: 2022-08-olympus/src/policies/PriceConfig.sol (line 45-74):

   `    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
        external
        onlyRole("price_admin")
    {
        PRICE.initialize(startObservations_, lastObservationTime_);
    }

    /// @notice                         Change the moving average window (duration)
    /// @param movingAverageDuration_   Moving average duration in seconds, must be a multiple of observation frequency
    /// @dev Setting the window to a larger number of observations than the current window will clear
    ///      the data in the current window and require the initialize function to be called again.
    ///      Ensure that you have saved the existing data and can re-populate before calling this
    ///      function with a number of observations larger than have been recorded.
    function changeMovingAverageDuration(uint48 movingAverageDuration_)
        external
        onlyRole("price_admin")
    {
        PRICE.changeMovingAverageDuration(movingAverageDuration_);
    }

    /// @notice   Change the observation frequency of the moving average (i.e. how often a new observation is taken)
    /// @param    observationFrequency_   Observation frequency in seconds, must be a divisor of the moving average duration
    /// @dev      Changing the observation frequency clears existing observation data since it will not be taken at the right time intervals.
    ///           Ensure that you have saved the existing data and/or can re-populate before calling this function.
    function changeObservationFrequency(uint48 observationFrequency_)
        external
        onlyRole("price_admin")
    {
        PRICE.changeObservationFrequency(observationFrequency_);
    }` 

11. File: 2022-08-olympus/src/policies/VoterRegistration.sol (line 45-56):

   `    function issueVotesTo(address wallet_, uint256 amount_) external onlyRole("voter_admin") {
        // Issue the votes in the VOTES module
        VOTES.mintTo(wallet_, amount_);
    }

    /// @notice Burn votes from a wallet
    /// @param  wallet_ - The address losing the votes.
    /// @param  amount_ - The amount of votes to burn from the wallet.
    function revokeVotesFrom(address wallet_, uint256 amount_) external onlyRole("voter_admin") {
        // Revoke the votes in the VOTES module
        VOTES.burnFrom(wallet_, amount_);
    }`






# [G-07] Bitshift for divide by 2 (When multiply or dividing by a power of two, it is cheaper to bitshift than to use standard math operations.):



1. File: 2022-08-olympus/src/policies/Operator.sol (line 372):

   `int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);`

2. File: 2022-08-olympus/src/policies/Operator.sol (line 427):

   `int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);`





# [G-08] Use simple comparison in trinary logic (The comparison operators >= and <= use more gas than >, 
<, or ==. Replacing the  >= and ≤ operators with a comparison 
operator that has an opcode in the EVM saves gas):



1. File: 2022-08-olympus/src/policies/Operator.sol (line 210-218):

   `           uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&
            _status.high.count >= config_.regenThreshold
        ) {
            _regenerate(true);
        }
        if (
            uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&
            _status.low.count >= config_.regenThreshold
        ) {`



# [G-09] Use calldata instead of memory for function parameters (Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.):



1. File: 2022-08-olympus/src/modules/PRICE.sol (line 205-208):

   ` function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
        external
        permissioned
    {`

2. File: 2022-08-olympus/src/policies/BondCallback.sol (line 152):

   `function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {`

3. File: 2022-08-olympus/src/policies/PriceConfig.sol (line 45):

   `function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)`

4. File: 2022-08-olympus/src/policies/Operator.sol (line 195):

   `function operate() external override onlyWhileActive onlyRole("operator_operate") {` 







# [G-10]  Amounts should be checked for 0 before calling a transfer (Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done at some places, it’s not consistently done in the solution.):



1. File: 2022-08-olympus/src/modules/TRSRY.sol (line 82):

   `token_.safeTransfer(to_, amount_);`

2. File: 2022-08-olympus/src/modules/TRSRY.sol (line 99):

   `token_.safeTransfer(msg.sender, amount_);`

3. File: 2022-08-olympus/src/modules/TRSRY.sol (line 110):

   `token_.safeTransferFrom(msg.sender, address(this), amount_);`

4. File: 2022-08-olympus/src/policies/Operator.sol (line 299):

   `ohm.safeTransferFrom(msg.sender, address(this), amountIn_);`       

5. File: 2022-08-olympus/src/policies/Operator.sol (line 330):

   `reserve.safeTransferFrom(msg.sender, address(TRSRY), amountIn_);`

6. File: 2022-08-olympus/src/policies/BondCallback.sol (line 124):

   `payoutToken.safeTransfer(msg.sender, outputAmount_);`

7. File: 2022-08-olympus/src/policies/BondCallback.sol (line 159):

   `token.safeTransfer(address(TRSRY), balance);` 

8. File: 2022-08-olympus/src/policies/Heart.sol (line 112):

   `rewardToken.safeTransfer(to_, reward);`

9. File: 2022-08-olympus/src/policies/Heart.sol (line 151):

   `token_.safeTransfer(msg.sender, token_.balanceOf(address(this)));` 

10. File: 2022-08-olympus/src/policies/Governance.sol (line 259):

   `VOTES.transferFrom(msg.sender, address(this), userVotes);`

11. File: 2022-08-olympus/src/policies/Governance.sol (line 312):

   `VOTES.transferFrom(address(this), msg.sender, userVotes);`          









# [G-11]  Using `bools` for storage incurs overhead.

While this is done at some places, it’s not consistently done in the solution.):



1. File: 2022-08-olympus/src/Kernel.sol (line 113):

   `bool public isActive;`

2. File: 2022-08-olympus/src/Kernel.sol (line 197):

   `mapping(Role => bool) public isRole;`

3. File: 2022-08-olympus/src/modules/PRICE.sol (line 62):

   `bool public initialized;`

4. File: 2022-08-olympus/src/policies/Operator.sol (line 63):

   `bool public initialized;`       

5. File: 2022-08-olympus/src/policies/Operator.sol (line 66):

   `bool public active;`

6. File: 2022-08-olympus/src/policies/BondCallback.sol (line 24):

   `mapping(address => mapping(uint256 => bool)) public approvedMarkets;`

7. File: 2022-08-olympus/src/policies/Heart.sol (line 33):

   `bool public active;`

8. File: 2022-08-olympus/src/policies/Governance.sol (line 105):

   `mapping(uint256 => bool) public proposalHasBeenActivated;`

9. File: 2022-08-olympus/src/policies/Governance.sol (line 117):

   `mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;  `





# [G-12]  Using `private` rather than `public` for constants, saves gas (If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable 
getter functions for deployment calldata, and not adding another entry 
to the method ID table):



1. File: 2022-08-olympus/src/modules/RANGE.sol (line 65):

   `uint256 public constant FACTOR_SCALE = 1e4;`

2. File: 2022-08-olympus/src/modules/PRICE.sol (line 59):

   `uint8 public constant decimals = 18;`

3. File: 2022-08-olympus/src/policies/Operator.sol (line 89):

   `uint32 public constant FACTOR_SCALE = 1e4;`

4. File: 2022-08-olympus/src/policies/Governance.sol (line 121-137):

   `    uint256 public constant SUBMISSION_REQUIREMENT = 100;

    /// @notice Amount of time a submitted proposal has to activate before it expires.
    uint256 public constant ACTIVATION_DEADLINE = 2 weeks;

    /// @notice Amount of time an activated proposal must stay up before it can be replaced by a new activated proposal.
    uint256 public constant GRACE_PERIOD = 1 weeks;

    /// @notice Endorsements required to activate a proposal as percentage of total supply.
    uint256 public constant ENDORSEMENT_THRESHOLD = 20;

    /// @notice Net votes required to execute a proposal on chain as a percentage of total supply.
    uint256 public constant EXECUTION_THRESHOLD = 33;

    /// @notice Required time for a proposal to be active before it can be executed.
    /// @dev    This amount should be greater than 0 to prevent flash loan attacks.
    uint256 public constant EXECUTION_TIMELOCK = 3 days;`












# [G-13]  Empty blocks should be removed or emit something [The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be `abstract` and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`)]:



1. File: 2022-08-olympus/src/Kernel.sol (line 85):

   `constructor(Kernel kernel_) KernelAdapter(kernel_) {}`

2. File: 2022-08-olympus/src/Kernel.sol (line 95):

   `function KEYCODE() public pure virtual returns (Keycode) {}`

3. File: 2022-08-olympus/src/Kernel.sol (line 100):

   `function VERSION() external pure virtual returns (uint8 major, uint8 minor) {};`

4. File: 2022-08-olympus/src/Kernel.sol (line 105):

   ` function INIT() external virtual onlyKernel {}`       

5. File: 2022-08-olympus/src/Kernel.sol (line 115):

   `constructor(Kernel kernel_) KernelAdapter(kernel_) {}`

6. File: 2022-08-olympus/src/Kernel.sol (line 139-143):

   `    function configureDependencies() external virtual returns (Keycode[] memory dependencies) {}

    /// @notice Function called by kernel to set module function permissions.
    /// @return requests - Array of keycodes and function selectors for requested permissions.
    function requestPermissions() external view virtual returns (Permissions[] memory requests) {}`     

7. File: 2022-08-olympus/src/modules/TRSRY.sol (line 45):

   `constructor(Kernel kernel_) Module(kernel_) {}`

8. File: 2022-08-olympus/src/modules/VOTES.sol (line 19):

   `{}`

9. File: 2022-08-olympus/src/modules/INSTR.sol (line 20):

   `constructor(Kernel kernel_) Module(kernel_) {}`

10. File: 2022-08-olympus/src/policies/TreasuryCustodian.sol (line 24):

   `constructor(Kernel kernel_) Policy(kernel_) {}`       

11. File: 2022-08-olympus/src/policies/PriceConfig.sol (line 15):

   `constructor(Kernel kernel_) Policy(kernel_) {}`

12. File: 2022-08-olympus/src/policies/Governance.sol (line 59):

   `constructor(Kernel kernel_) Policy(kernel_) {}`         

13. File: 2022-08-olympus/src/policies/VoterRegistration.sol (line 16):

   `constructor(Kernel kernel_) Policy(kernel_) {}`

