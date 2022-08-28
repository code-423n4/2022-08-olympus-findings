# [L-01]  Unsafe calls to optional ERC20 functions (`decimals()`, `name()` and `symbol()`
 are optional parts of the ERC20 specification, so there are tokens that
 do not implement them. It’s not safe to cast arbitrary token addresses 
in order to call these functions. If `IERC20Metadata` is to be relied on, that should be the variable type of the token variable, rather than it being `address`, so the compiler can verify that types correctly match, rather than this being a runtime failure. See [this](https://github.com/code-423n4/2021-05-yield-findings/issues/32) prior instance of [this](https://github.com/boringcrypto/BoringSolidity/blob/c73ed73afa9273fbce93095ef177513191782254/contracts/libraries/BoringERC20.sol#L49-L55) issue which was marked as Low risk. Do this to resolve the issue.):



1. File: 2022-08-olympus/src/modules/PRICE.sol (line 84):

   `uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();`

2. File: 2022-08-olympus/src/modules/PRICE.sol (line 87):

   `uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();`

3. File: 2022-08-olympus/src/policies/Operator.sol (line 122):

   `ohmDecimals = tokens_[0].decimals();`

4. File: 2022-08-olympus/src/policies/Operator.sol (line 124):

   `reserveDecimals = tokens_[1].decimals();`       

5. File: 2022-08-olympus/src/policies/Operator.sol (line 418):

   `uint8 oracleDecimals = PRICE.decimals();`

6. File: 2022-08-olympus/src/policies/Operator.sol (line 493):

   `return decimals - int8(PRICE.decimals());`










# [L-02]  Missing checks for `address(0x0)` when assigning values to `address` state variables:



1. File: 2022-08-olympus/src/Kernel.sol (line 66):

   `kernel = kernel_;`

2. File: 2022-08-olympus/src/Kernel.sol (line 77):

   `kernel = newKernel_;`

3. File: 2022-08-olympus/src/Kernel.sol (line 127):

   `isActive = activate_;`

4. File: 2022-08-olympus/src/modules/RANGE.sol (line 265):

   `thresholdFactor = thresholdFactor_;`       

5. File: 2022-08-olympus/src/policies/BondCallback.sol (line 43-44):

   `        aggregator = aggregator_;
        ohm = ohm_;`


       


# [L-03]  initialize functions can be front-run (See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details):



1. File: 2022-08-olympus/src/modules/PRICE.sol (line 205):

   `function initialize(`

2. File: 2022-08-olympus/src/policies/Operator.sol (line 598):

   `function initialize()`

3. File: 2022-08-olympus/src/policies/PriceConfig.sol (line 45):

   `function initialize(`

4. File: 2022-08-olympus/src/policies/interfaces/IOperator.sol (line 125):

   `function initialize(`




# Non-Critical Issues



# [N-01] dding a `return` statement when the function defines a named return variable, is redundant:



1. File: 2022-08-olympus/src/modules/RANGE.sol (line 276):

`return _range;`

2. File: 2022-08-olympus/src/policies/Operator.sol (line 784):

`return _status;`

3. File: 2022-08-olympus/src/policies/Operator.sol (line 799):

` return _config;`




# [N-02]  constants should be defined rather than using magic numbers:



1. File: 2022-08-olympus/src/utils/KernelUtils.sol (line 46):

   `if (char < 0x41 || char > 0x5A) revert InvalidKeycode(keycode_);`

2. File: 2022-08-olympus/src/utils/KernelUtils.sol (line 60):

   `if ((char < 0x61 || char > 0x7A) && char != 0x5f && char != 0x00) {`

3. File: 2022-08-olympus/src/modules/RANGE.sol (line 65):

   `uint256 public constant FACTOR_SCALE = 1e4;`

4. File: 2022-08-olympus/src/modules/RANGE.sol (line 79-80):

   `        ERC20[2] memory tokens_,
        uint256[3] memory rangeParams_`       

5. File: 2022-08-olympus/src/modules/RANGE.sol (line 43-44):

   `            wallSpread_ > 10000 ||
            wallSpread_ < 100 ||
            cushionSpread_ > 10000 ||
            cushionSpread_ < 100 ||`

6. File: 2022-08-olympus/src/modules/RANGE.sol (line 264):

   `if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();`  

7. File: 2022-08-olympus/src/modules/PRICE.sol (line 59):

   `uint8 public constant decimals = 18;`

8. File: 2022-08-olympus/src/modules/PRICE.sol (line 90-91):

   `        if (exponent > 38) revert Price_InvalidParams();
        _scaleFactor = 10**exponent;`

9. File: 2022-08-olympus/src/modules/PRICE.sol (line 165):

   `if (updatedAt < block.timestamp - 3 * uint256(observationFrequency))`

10. File: 2022-08-olympus/src/policies/Operator.sol (line 79-80):

   `uint32 public constant FACTOR_SCALE = 1e4;`       

11. File: 2022-08-olympus/src/policies/Operator.sol (line 106):

   `if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();`

12. File: 2022-08-olympus/src/policies/Operator.sol (line 111):

   `if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();`

13. File: 2022-08-olympus/src/policies/Operator.sol (line 375-379):

   `            uint256 oracleScale = 10**uint8(int8(PRICE.decimals()) - priceDecimals);
            uint256 bondScale = 10 **
                uint8(
                    36 + scaleAdjustment + int8(reserveDecimals) - int8(ohmDecimals) - priceDecimals
                );`       

14. File: 2022-08-olympus/src/policies/Operator.sol (line 419-434):

   `            uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
            uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;

            /// Calculate scaleAdjustment for bond market
            /// Price decimals are returned from the perspective of the quote token
            /// so the operations assume payoutPriceDecimal is zero and quotePriceDecimals
            /// is the priceDecimal value
            int8 priceDecimals = _getPriceDecimals(invCushionPrice);
            int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);

            /// Calculate oracle scale and bond scale with scale adjustment and format prices for bond market
            uint256 oracleScale = 10**uint8(int8(oracleDecimals) - priceDecimals);
            uint256 bondScale = 10 **
                uint8(
                    36 + scaleAdjustment + int8(ohmDecimals) - int8(reserveDecimals) - priceDecimals
                );`

15. File: 2022-08-olympus/src/policies/Operator.sol (line 518):

   `if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();`

16. File: 2022-08-olympus/src/policies/Operator.sol (line 535):

   `if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();`

17. File: 2022-08-olympus/src/policies/Operator.sol (line 550):

   `if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();`

18. File: 2022-08-olympus/src/policies/Operator.sol (line 753-766):

   `                10**reserveDecimals * RANGE.price(true, false),
                10**ohmDecimals * 10**PRICE.decimals()
            );

            /// Revert if amount out exceeds capacity
            if (amountOut > RANGE.capacity(false)) revert Operator_InsufficientCapacity();

            return amountOut;
        } else if (tokenIn_ == reserve) {
            /// Calculate amount out
            uint256 amountOut = amountIn_.mulDiv(
                10**ohmDecimals * 10**PRICE.decimals(),
                10**reserveDecimals * RANGE.price(true, true)
            );`

19. File: 2022-08-olympus/src/policies/Operator.sol (line 784-786):

   `                    10**ohmDecimals * 10**PRICE.decimals(),
                    10**reserveDecimals * RANGE.price(true, true)
                ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /`

20. File: 2022-08-olympus/src/policies/Governance.sol (line 268):

   `if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {`       








# [N-03]  `Event` is missing `indexed` fields (Each `event` should use three `indexed` fields if there are three or more fields):



1. File: 2022-08-olympus/src/Kernel.sol (line 203-208):

   `   event PermissionsUpdated(
        Keycode indexed keycode_,
        Policy indexed policy_,
        bytes4 funcSelector_,
        bool granted_
    );`

2. File: 2022-08-olympus/src/modules/TRSRY.sol (line 20):

   `event ApprovedForWithdrawal(address indexed policy_, ERC20 indexed token_, uint256 amount_);`

3. File: 2022-08-olympus/src/modules/TRSRY.sol (line 27-29):

   `    event DebtIncurred(ERC20 indexed token_, address indexed policy_, uint256 amount_);
    event DebtRepaid(ERC20 indexed token_, address indexed policy_, uint256 amount_);
    event DebtSet(ERC20 indexed token_, address indexed policy_, uint256 amount_);`

4. File: 2022-08-olympus/src/modules/RANGE.sol (line 20-31):

   `    event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);
    event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);
    event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);
    event CushionDown(bool high_, uint256 timestamp_);
    event PricesChanged(
        uint256 wallLowPrice_,
        uint256 cushionLowPrice_,
        uint256 cushionHighPrice_,
        uint256 wallHighPrice_
    );
    event SpreadsChanged(uint256 cushionSpread_, uint256 wallSpread_);
    event ThresholdFactorChanged(uint256 thresholdFactor_);`       

5. File: 2022-08-olympus/src/modules/PRICE.sol (line 26):

   `event NewObservation(uint256 timestamp_, uint256 price_, uint256 movingAverage_);`

6. File: 2022-08-olympus/src/policies/Operator.sol (line 45-50):

   ` event Swap(
        ERC20 indexed tokenIn_,
        ERC20 indexed tokenOut_,
        uint256 amountIn_,
        uint256 amountOut_
    );`  

7. File: 2022-08-olympus/src/policies/Operator.sol (line 52):

   `event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);`

8. File: 2022-08-olympus/src/policies/Operator.sol (line 54):

   `event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);`

9. File: 2022-08-olympus/src/policies/Heart.sol (line 28-30):

   `    event RewardIssued(address to_, uint256 rewardAmount_);
    event RewardUpdated(ERC20 token_, uint256 rewardAmount_);`

10. File: 2022-08-olympus/src/policies/Governance.sol (line 87-89):

   `    event ProposalEndorsed(uint256 proposalId, address voter, uint256 amount);
    event ProposalActivated(uint256 proposalId, uint256 timestamp);
    event WalletVoted(uint256 proposalId, address voter, bool for_, uint256 userVotes);`






# [N-04]  Use of sensitive/non-inclusive terms:



1. File: 2022-08-olympus/src/modules/RANGE.sol (line 44):

`bool active;`

2. File: 2022-08-olympus/src/policies/interfaces/IOperator.sol (line 34):

`bool[] observations;`




  
   
# [N-05]  Open TODOs (Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment):



1. File: 022-08-olympus/src/policies/TreasuryCustodian.sol (line 51-52):

`    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
    // TODO must reorg policy storage to be able to check for deactivated policies.`

2. File: 022-08-olympus/src/policies/TreasuryCustodian.sol (line 56):

`// TODO Make sure `policy_` is an actual policy and not a random address.`

3. File: 2022-08-olympus/src/policies/Operator.sol (line 657):

`/// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?`








# [N-06]  `public` functions not called by the contract should be declared `external` instead (Contracts are allowed to override their parents’ functions and change the visibility from `public` to `external`.):



1. File: 2022-08-olympus/src/Kernel.sol (line 95):

`function KEYCODE() public pure virtual returns (Keycode) {}`

2. File: 2022-08-olympus/src/Kernel.sol (line 439):

`function grantRole(Role role_, address addr_) public onlyAdmin {`

3. File: 2022-08-olympus/src/Kernel.sol (line 451):

`function revokeRole(Role role_, address addr_) public onlyAdmin {`

4. File: 2022-08-olympus/src/modules/TRSRY.sol (line 75-79):

`  function withdrawReserves(
        address to_,
        ERC20 token_,
        uint256 amount_
    ) public {`       

5. File: 2022-08-olympus/src/modules/MINTR.sol (line 33-39):

`   function mintOhm(address to_, uint256 amount_) public permissioned {
        ohm.mint(to_, amount_);
    }

    function burnOhm(address from_, uint256 amount_) public permissioned {
        ohm.burnFrom(from_, amount_);
    }`

6. File: 2022-08-olympus/src/modules/PRICE.sol (line 154):

`function getCurrentPrice() public view returns (uint256) {`

7. File: 2022-08-olympus/src/policies/Heart.sol (line 37):

`function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {`

8. File: 2022-08-olympus/src/policies/Governance.sol (line 121):

`function frequency() public view returns (uint256) {`

9. File: 2022-08-olympus/src/policies/Governance.sol (line 145-153):

`    function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {
        return getProposalMetadata[proposalId_];
    }

    /// @notice Return the currently active proposal in governance.
    /// @dev    Used to return & access the entire struct active proposal struct in solidity.
    function getActiveProposal() public view returns (ActivatedProposal memory) {
        return activeProposal;
    }`
    



# [N-07]  Numeric values having to do with time should use time units for readability (There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks):



1. File: 2022-08-olympus/src/Kernel.sol (line 121-137):

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








