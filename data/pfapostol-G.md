##### Summary

Gas savings are estimated using the gas report of existing `FORGE_GAS_REPORT=true forge test` tests (the sum of all deployment costs and the sum of the costs of calling all methods) and may vary depending on the implementation of the fix. I keep my version of the fix for each finding and can provide them if you need.
Some optimizations (mostly logical) cannot be scored with a exact gas quantity.

Gas Optimizations
||Issue|Instances|Estimated gas(deployments)|Estimated gas(method call)|
|:---:|:---|:---:|:---:|:---:|
|**1**|Replace `modifier` with `function`|6|460 154|-|
|**2**|`storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping`|7|188 639|5 032|
|**3**|Using `private` rather than `public` for constants, saves gas|8|45 857|308|
|**4**|Use elementary types or a user-defined `type` instead of a `struct` that has only one member|1|30 714|1 037|
|**5**|State variables should be cached in stack variables rather than re-reading them from storage|7|24 021|614|
|**6**|Using bools for storage incurs overhead|6|23 611|4 485|
|**7**|State variables can be packed into fewer storage slots|3|23 292|1 711|
|**8**|Expressions that cannot be overflowed can be unchecked|5|23 016|-|
|**9**|Increment optimization|18|↓|↓|
|**9.1**|Prefix increments are cheaper than postfix increments, especially when it's used in for-loops|3|400|-|
|**9.2**|`<x> = <x> + 1` even more efficient than pre increment|18|14 217|-|
|**10**|Use named `returns` for local variables where it is possible|3|5 400|-|
|**11**|`x = x + y` is cheaper than `x += y;`|6|5 000|-|
|**12**|Deleting a struct is cheaper than creating a new struct with null values.|1|4 207|-|
|**13**|Don't compare boolean expressions to boolean literals|2|1 607|-|
|**14**|`revert` operator should be in the code as early as reasonably possible|3|200|1 559+|
|**15**|Duplicated require()/revert() checks should be refactored to a modifier or function|4|-|8 111|

**Total: 83 instances over 15 issues**

---

1. **Replace `modifier` with `function` (6 instances)**

   modifiers make code more elegant, but cost more than normal functions

   Deployment Gas Saved: **460 154**

   All modifiers except `permissioned` due to unresolved error flow

   - src/Kernel.sol:[70-73](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L70-L73), [119-123](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L119-L123), [223-232](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L223-L232)

   ```solidity
   70     modifier onlyKernel() {
   71         if (msg.sender != address(kernel)) revert KernelAdapter_OnlyKernel(msg.sender);
   72         _;
   73     }
   ...
   119    modifier onlyRole(bytes32 role_) {
   120        Role role = toRole(role_);
   121        if (!kernel.hasRole(msg.sender, role)) revert Policy_OnlyRole(role);
   122        _;
   123    }
   ...
   223    modifier onlyExecutor() {
   224        if (msg.sender != executor) revert Kernel_OnlyExecutor(msg.sender);
   225        _;
   226    }
   227
   228    /// @notice Modifier to check if caller is the roles admin.
   229    modifier onlyAdmin() {
   230        if (msg.sender != admin) revert Kernel_OnlyAdmin(msg.sender);
   231        _;
   232    }
   ```

   - src/policies/Operator.sol:[188-191](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L188-L191)

   ```solidity
   188    modifier onlyWhileActive() {
   189        if (!active) revert Operator_Inactive();
   190        _;
   191    }
   ```

   - [src/modules/PRICE.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol)

   ```solidity
   if (!initialized) revert Price_NotInitialized(); // @note 4 instances
   ```

2. **`storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` (7 instances)**

   Deployment Gas Saved: **188 639**
   Method Call Gas Saved: **5 032**

   It may not be obvious, but every time you copy a storage `struct`/`array`/`mapping` to a `memory` variable, you are literally copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the storage, which is much cheaper.

   - src/Kernel.sol:[379](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L379)

   ```solidity
   379        Policy[] memory dependents = moduleDependents[keycode_];
   ```

   fix(the same for others):

   ```solidity
   Policy[] storage dependents = moduleDependents[keycode_];
   ```

   - src/policies/BondCallback.sol:[179](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L179)

   ```solidity
   179        uint256[2] memory marketAmounts = _amountsPerMarket[id_];
   ```

   - src/policies/Governance.sol:[206](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L206)

   ```solidity
   206        ProposalMetadata memory proposal = getProposalMetadata[proposalId_];
   ```

   - src/policies/Operator.sol:[205-206](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L205-L206), [384-385](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L384-L385), [439-440](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L439-L440), [666](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L666)

   ```solidity
   205        /// Cache config in memory
   206        Config memory config_ = _config;
   ...
   384            /// Cache config struct to avoid multiple SLOADs
   385            Config memory config_ = _config;
   ...
   439            /// Cache config struct to avoid multiple SLOADs
   440            Config memory config_ = _config;
   ...
   666        Regen memory regen = _status.low;
   ```

3. **Using `private` rather than `public` for constants, saves gas (8 instances)**

   If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

   Deployment Gas Saved: **45 857**
   Method Call Gas Saved: **308**

   - src/policies/Governance.sol:[119-137](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L119-L137)

   ```solidity
   119    /// @notice The amount of votes a proposer needs in order to submit a proposal as a percentage of total supply (in basis points).
   120    /// @dev    This is set to 1% of the total supply.
   121    uint256 public constant SUBMISSION_REQUIREMENT = 100;
   122
   123    /// @notice Amount of time a submitted proposal has to activate before it expires.
   124    uint256 public constant ACTIVATION_DEADLINE = 2 weeks;
   125
   126    /// @notice Amount of time an activated proposal must stay up before it can be replaced by a new activated proposal.
   127    uint256 public constant GRACE_PERIOD = 1 weeks;
   128
   129    /// @notice Endorsements required to activate a proposal as percentage of total supply.
   130    uint256 public constant ENDORSEMENT_THRESHOLD = 20;
   131
   132    /// @notice Net votes required to execute a proposal on chain as a percentage of total supply.
   133    uint256 public constant EXECUTION_THRESHOLD = 33;
   134
   135    /// @notice Required time for a proposal to be active before it can be executed.
   136    /// @dev    This amount should be greater than 0 to prevent flash loan attacks.
   137    uint256 public constant EXECUTION_TIMELOCK = 3 days;
   ```

   - src/policies/Operator.sol:[89](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L89)

   ```solidity
   89     uint32 public constant FACTOR_SCALE = 1e4;
   ```

   - src/modules/RANGE.sol:[65](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L65)

   ```solidity
   65     uint256 public constant FACTOR_SCALE = 1e4;
   ```

4. **Use elementary types or a user-defined `type` instead of a `struct` that has only one member. (1 instances)**

   Deployment Gas Saved: **30 714**
   Method Call Gas Saved: **1 037**

   - src/modules/RANGE.sol:[33-35](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L33-L35)

   ```solidity
   33     struct Line {
   34         uint256 price; // Price for the specified level
   35     }
   ```

5. **State variables should be cached in stack variables rather than re-reading them from storage**

   Deployment Gas Saved: **24 021**
   Method Call Gas Saved: **614**

   SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

   - src/policies/Heart.sol:[112-113](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L112-L113)

   ```solidity
   112        rewardToken.safeTransfer(to_, reward);
   113        emit RewardIssued(to_, reward);
   ```

   fix:

   ```solidity
           uint256 reward = reward;
           rewardToken.safeTransfer(to_, reward);
           emit RewardIssued(to_, reward);
   ```

   - src/policies/BondCallback.sol:[68-75](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L68-L75)

   ```solidity
   68         Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();
   69         Keycode MINTR_KEYCODE = MINTR.KEYCODE();
   70
   71         requests = new Permissions[](4);
   72         requests[0] = Permissions(TRSRY_KEYCODE, TRSRY.setApprovalFor.selector);
   73         requests[1] = Permissions(TRSRY_KEYCODE, TRSRY.withdrawReserves.selector);
   74         requests[2] = Permissions(MINTR_KEYCODE, MINTR.mintOhm.selector);
   75         requests[3] = Permissions(MINTR_KEYCODE, MINTR.burnOhm.selector);
   ```

   fix(similar for other policies):

   ```solidity
       OlympusTreasury ltrsry = TRSRY;
       OlympusMinter lmintr = MINTR;
       Keycode TRSRY_KEYCODE = ltrsry.KEYCODE();
       Keycode MINTR_KEYCODE = lmintr.KEYCODE();

       requests = new Permissions[](4);

       requests[0] = Permissions(TRSRY_KEYCODE, ltrsry.setApprovalFor.selector);
       requests[1] = Permissions(TRSRY_KEYCODE, ltrsry.withdrawReserves.selector);
       requests[2] = Permissions(MINTR_KEYCODE, lmintr.mintOhm.selector);
       requests[3] = Permissions(MINTR_KEYCODE, lmintr.burnOhm.selector);
   ```

   - src/policies/Governance.sol:[77-79](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L77-L79)

   ```solidity
   77         requests = new Permissions[](2);
   78         requests[0] = Permissions(INSTR.KEYCODE(), INSTR.store.selector);
   79         requests[1] = Permissions(VOTES.KEYCODE(), VOTES.transferFrom.selector);
   ```

   - src/policies/Operator.sol:[172-185](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L172-L185)

   ```solidity
   172        Keycode RANGE_KEYCODE = RANGE.KEYCODE();
   173        Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();
   174        Keycode MINTR_KEYCODE = MINTR.KEYCODE();
   175
   176        requests = new Permissions[](9);
   177        requests[0] = Permissions(RANGE_KEYCODE, RANGE.updateCapacity.selector);
   178        requests[1] = Permissions(RANGE_KEYCODE, RANGE.updateMarket.selector);
   179        requests[2] = Permissions(RANGE_KEYCODE, RANGE.updatePrices.selector);
   180        requests[3] = Permissions(RANGE_KEYCODE, RANGE.regenerate.selector);
   181        requests[4] = Permissions(RANGE_KEYCODE, RANGE.setSpreads.selector);
   182        requests[5] = Permissions(RANGE_KEYCODE, RANGE.setThresholdFactor.selector);
   183        requests[6] = Permissions(TRSRY_KEYCODE, TRSRY.setApprovalFor.selector);
   184        requests[7] = Permissions(MINTR_KEYCODE, MINTR.mintOhm.selector);
   185        requests[8] = Permissions(MINTR_KEYCODE, MINTR.burnOhm.selector);
   ```

   - src/policies/PriceConfig.sol:[32-34](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/PriceConfig.sol#L32-L34)

   ```solidity
   32        permissions[0] = Permissions(PRICE.KEYCODE(), PRICE.initialize.selector);
   33        permissions[1] = Permissions(PRICE.KEYCODE(), PRICE.changeMovingAverageDuration.selector);
   34        permissions[2] = Permissions(PRICE.KEYCODE(), PRICE.changeObservationFrequency.selector);
   ```

   - src/policies/TreasuryCustodian.sol:[35-39](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol#L35-L39)

   ```solidity
   35        Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();
   36
   37        requests = new Permissions[](2);
   38        requests[0] = Permissions(TRSRY_KEYCODE, TRSRY.setApprovalFor.selector);
   39        requests[1] = Permissions(TRSRY_KEYCODE, TRSRY.setDebt.selector);
   ```

   - src/policies/VoterRegistration.sol:[33-35](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/VoterRegistration.sol#L34-L35)

   ```solidity
   33        permissions = new Permissions[](2);
   34        permissions[0] = Permissions(VOTES.KEYCODE(), VOTES.mintTo.selector);
   35        permissions[1] = Permissions(VOTES.KEYCODE(), VOTES.burnFrom.selector);
   ```

6. **Using bools for storage incurs overhead (6 instances)**

   Deployment Gas Saved: **23 611**
   Method Call Gas Saved: **4 485**

   ```
   // Booleans are more expensive than uint256 or any type that takes up a full
   // word because each write operation emits an extra SLOAD to first read the
   // slot's contents, replace the bits taken up by the boolean, and then write
   // back. This is the compiler's defense against contract upgrades and
   // pointer aliasing, and it cannot be disabled.
   ```

   Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

   **Important**: This rule doesn't always work, sometimes a bool is packed with another variable in the same slot, sometimes it's packed into a struct, sometimes the optimizer makes bool more efficient. You can see the @note in the code for each case

   - src/Kernel.sol:[181](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L181), [194](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L194), [197](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L197)

   ```solidity
   181    mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions; //@note D:3200 M:1754
   ...
   194    mapping(address => mapping(Role => bool)) public hasRole; //@note D:−3016 M:2298
   ...
   197    mapping(Role => bool) public isRole; //@note D:2407
   ```

   - src/policies/Governance.sol:[105](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L105), [117](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L117),

   ```solidity
   105    mapping(uint256 => bool) public proposalHasBeenActivated; //@note D:3007
   ...
   117    mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal; //@note D:3007
   ```

   - src/modules/PRICE.sol:[62](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L62)

   ```solidity
   62     bool public initialized; //@note D:11813
   ```

   **Expensive method calls**:

   It's just to show which bool is better left in the code

   - src/policies/Operator.sol

   ```solidity
   63     bool public initialized; //@note D:5808 M:-22036
   ...
   66     bool public active; //@note D:-32775 M:-48896
   ```

   - src/policies/Heart.sol

   ```solidity
   33     bool public active; //@note D:-382
   ```

   - src/policies/BondCallback.sol

   ```solidity
   24     mapping(address => mapping(uint256 => bool)) public approvedMarkets; //@note D:-44192
   ```

   - src/Kernel.sol

   ```solidity
   113    bool public isActive; //@note D:20923 M:-247184
   ```

7. **State variables can be packed into fewer storage slots (3 instances)**

   If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper

   **NOTE**: one slot = 32 bytes

   Deployment Gas Saved: **23 292**
   Method Call Gas Saved: **1 711**

   - src/policies/Heart.sol:[32-48](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L32-L48)

   uint256(32), address(20), bool(1)

   ```solidity
   32     /// @notice Status of the Heart, false = stopped, true = beating
   33     bool public active; // @note put below _operator
   34
   35     /// @notice Timestamp of the last beat (UTC, in seconds)
   36     uint256 public lastBeat;
   37
   38     /// @notice Reward for beating the Heart (in reward token decimals)
   39     uint256 public reward;
   40
   41     /// @notice Reward token address that users are sent for beating the Heart
   42     ERC20 public rewardToken;
   43
   44     // Modules
   45     OlympusPrice internal PRICE;
   46
   47     // Policies
   48     IOperator internal _operator;
   ```

   fix:

   ```solidity
   uint256 public lastBeat;
   uint256 public reward;
   ERC20 public rewardToken;
   OlympusPrice internal PRICE;
   IOperator internal _operator;
   bool public active;
   ```

   - src/modules/PRICE.sol:[31-65](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L31-L65)

   **NOTE**: PRICE is Module, Module is KernelAdapter, so real first variable in PRICE is kernel from KernelAdapter

   uint256(32), uint32(4), uint48(6), uint8(1), array(32), address(20), bool(1)

   ```solidity
   inherit Kernel public kernel;
   ...
   31     /// @dev    Price feeds. Chainlink typically provides price feeds for an asset in ETH. Therefore, we use two price feeds against ETH, one for OHM and one for the Reserve asset, to calculate the relative price of OHM in the Reserve asset.
   32     AggregatorV2V3Interface internal immutable _ohmEthPriceFeed;
   33     AggregatorV2V3Interface internal immutable _reserveEthPriceFeed;
   34
   35     /// @dev Moving average data
   36     uint256 internal _movingAverage; /// See getMovingAverage()
   37
   38     /// @notice Array of price observations. Check nextObsIndex to determine latest data point.
   39     /// @dev    Observations are stored in a ring buffer where the moving average is the sum of all observations divided by the number of observations.
   40     ///         Observations can be cleared by changing the movingAverageDuration or observationFrequency and must be re-initialized.
   41     uint256[] public observations;
   42
   43     /// @notice Index of the next observation to make. The current value at this index is the oldest observation.
   44     uint32 public nextObsIndex;
   45
   46     /// @notice Number of observations used in the moving average calculation. Computed from movingAverageDuration / observationFrequency.
   47     uint32 public numObservations;
   48
   49     /// @notice Frequency (in seconds) that observations should be stored.
   50     uint48 public observationFrequency;
   51
   52     /// @notice Duration (in seconds) over which the moving average is calculated.
   53     uint48 public movingAverageDuration;
   54
   55     /// @notice Unix timestamp of last observation (in seconds).
   56     uint48 public lastObservationTime;
   57
   58     /// @notice Number of decimals in the price values provided by the contract.
   59     uint8 public constant decimals = 18;
   60
   61     /// @notice Whether the price module is initialized (and therefore active).
   62     bool public initialized;
   63
   64     // Scale factor for converting prices, calculated from decimal values.
   65     uint256 internal immutable _scaleFactor;
   ```

   fix:

   ```solidity
   uint48 public observationFrequency;
   uint48 public movingAverageDuration;
   AggregatorV2V3Interface internal immutable _ohmEthPriceFeed;
   AggregatorV2V3Interface internal immutable _reserveEthPriceFeed;
   uint256 internal _movingAverage; /// See getMovingAverage()
   uint256[] public observations;
   uint32 public nextObsIndex;
   uint32 public numObservations;
   uint48 public lastObservationTime;
   uint8 public constant decimals = 18;
   bool public initialized;
   uint256 internal immutable _scaleFactor;
   ```

   - src/policies/Operator.sol:[58-89](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L58-L89)

   uint32(4), uint8(1), address(20), bool(1)

   ```solidity
   58     /// Operator variables, defined in the interface on the external getter functions
   59     Status internal _status;
   60     Config internal _config;
   61
   62     /// @notice    Whether the Operator has been initialized
   63     bool public initialized;
   64
   65     /// @notice    Whether the Operator is active
   66     bool public active;
   67
   68     /// Modules
   69     OlympusPrice internal PRICE;
   70     OlympusRange internal RANGE;
   71     OlympusTreasury internal TRSRY;
   72     OlympusMinter internal MINTR;
   73
   74     /// External contracts
   75     /// @notice     Auctioneer contract used for cushion bond market deployments
   76     IBondAuctioneer public auctioneer;
   77     /// @notice     Callback contract used for cushion bond market payouts
   78     IBondCallback public callback;
   79
   80     /// Tokens
   81     /// @notice     OHM token contract
   82     ERC20 public immutable ohm;
   83     uint8 public immutable ohmDecimals;
   84     /// @notice     Reserve token contract
   85     ERC20 public immutable reserve;
   86     uint8 public immutable reserveDecimals;
   87
   88     /// Constants
   89     uint32 public constant FACTOR_SCALE = 1e4;
   ```

   fix:

   ```solidity
   Status internal _status;
   Config internal _config;
   OlympusPrice internal PRICE;
   OlympusRange internal RANGE;
   OlympusTreasury internal TRSRY;
   OlympusMinter internal MINTR;
   IBondAuctioneer public auctioneer;
   IBondCallback public callback;
   bool public initialized;
   bool public active;
   ERC20 public immutable ohm;
   uint8 public immutable ohmDecimals;
   ERC20 public immutable reserve;
   uint8 public immutable reserveDecimals;
   uint32 public constant FACTOR_SCALE = 1e4;
   ```

8. **Expressions that cannot be overflowed can be unchecked (5 instances)**

   Deployment Gas Saved: **23 016**

   - src/Kernel.sol:[299-300](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L299-L300), [309-310](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L309-L310)

   ```solidity
   299        activePolicies.push(policy_);
   300        getPolicyIndex[policy_] = activePolicies.length - 1; // @note cannot be overflowed due to a previous push
   ...
   309            moduleDependents[keycode].push(policy_);
   310            getDependentIndex[keycode][policy_] = moduleDependents[keycode].length - 1; // @note cannot be overflowed due to a previous push
   ```

   - src/modules/PRICE.sol:[89](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L89), [144](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L144), [171](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L171)

   ```solidity
   89         uint256 exponent = decimals + reserveEthDecimals - ohmEthDecimals; //@note overflow is not possible, if an underflow occurs, the next statement will revert
   ...
   144        nextObsIndex = (nextObsIndex + 1) % numObs; //@note numObs can not be equal 0 during to check in constructor
   ...
   171            if (updatedAt < block.timestamp - uint256(observationFrequency)) // @note can not be underflowed due to ` - 3 * uint256(observationFrequency)` in 165
   ```

9. **Increment optimization (18 instances)**

   For a uint256 i variable, the following is true with the Optimizer enabled at 10k:

   Increment:

   i += 1 is the most expensive form
   i++ costs 6 gas less than i += 1
   ++i costs 5 gas less than i++ (11 gas less than i += 1)
   Decrement:

   i -= 1 is the most expensive form
   i-- costs 11 gas less than i -= 1
   --i costs 5 gas less than i-- (16 gas less than i -= 1)

   1. **Prefix increments are cheaper than postfix increments, especially when it's used in for-loops (3 instances).**

   Deployment Gas Saved: **400**

   - src/utils/KernelUtils.sol:[49](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L49), [64](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L64)

   ```solidity
   49            i++;
   ...
   64            i++;
   ```

   - src/policies/Operator.sol:[488](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L488)

   NOTE: in case of 670 675 686 691 not applicable and gas will be lost

   ```solidity
   488            decimals++;
   ```

   2. **`<x> = <x> + 1` even more efficient than pre increment.(18 instances)**

   Deployment Gas Saved: **14 217**

   - src/utils/KernelUtils.sol:[49](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L49), [64](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L64)

   ```solidity
   49            i++;
   ...
   64            i++;
   ```

   - src/policies/Operator.sol:[488](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L488), [670](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L670), [675](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L675), [686](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L686), [691](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L691)
   -

   ```solidity
   488            decimals++;
   ...
   670                _status.low.count++;
   ...
   675                _status.low.count--;
   ...
   686                _status.high.count++;
   ...
   691                _status.high.count--;
   ```

   - src/Kernel.sol:[313](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L313), [357](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L357), [369](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L369), [386](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L386), [404](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L404), [429](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L429)

   ```solidity
   313                ++i;
   ...
   357                ++i;
   ...
   369                ++j;
   ...
   386                ++i;
   ...
   404                ++i;
   ...
   429                ++i;
   ```

   - src/modules/INSTR.sol:[72](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/INSTR.sol#L72)

   ```solidity
   72                ++i;
   ```

   - src/modules/PRICE.sol:[225](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L225)

   ```solidity
   225                ++i;
   ```

   - src/policies/BondCallback.sol:[163](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L163)

   ```solidity
   163                ++i;
   ```

   - src/policies/Governance.sol:[281](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L281)

   ```solidity
   281                ++step;
   ```

   - src/policies/TreasuryCustodian.sol:[62](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol#L62)

   ```solidity
   62                ++j;
   ```

10. **Use named `returns` for local variables where it is possible (3 instances)**

    Deployment Gas Saved: **5 400**

    - src/Kernel.sol:[130-135](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L130-L135)

    ```solidity
    130    /// @notice Function to grab module address from a given keycode.
    131    function getModuleAddress(Keycode keycode_) internal view returns (address) {
    132        address moduleForKeycode = address(kernel.getModuleForKeycode(keycode_));
    133        if (moduleForKeycode == address(0)) revert Policy_ModuleDoesNotExist(keycode_);
    134        return moduleForKeycode;
    135    }
    ```

    fix:

    ```solidity
     function getModuleAddress(Keycode keycode_) internal view returns (address moduleForKeycode) {
         moduleForKeycode = address(kernel.getModuleForKeycode(keycode_));
         if (moduleForKeycode == address(0)) revert Policy_ModuleDoesNotExist(keycode_);
     }
    ```

    - src/modules/INSTR.sol:[41-79](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/INSTR.sol#L41-L79)

    ```solidity
    41    /// @notice Store a list of Instructions to be executed in the future.
    42    function store(Instruction[] calldata instructions_) external permissioned returns (uint256) {
    43        uint256 length = instructions_.length;
    44        uint256 instructionsId = ++totalInstructions;
    45
    46        Instruction[] storage instructions = storedInstructions[instructionsId];
    ...
    76        emit InstructionsStored(instructionsId);
    77
    78        return instructionsId;
    79    }
    ```

    - src/modules/PRICE.sol:[153-180](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L153-L180)

    ```solidity
    153    /// @notice Get the current price of OHM in the Reserve asset from the price feeds
    154    function getCurrentPrice() public view returns (uint256) {
    ...
    177        uint256 currentPrice = (ohmEthPrice * _scaleFactor) / reserveEthPrice;
    178
    179        return currentPrice;
    180    }
    ```

11. **`x = x + y` is cheaper than `x += y;` (6 instances)**

    Deployment Gas Saved: **5 000**

    Usually does not work with struct and mappings

    - src/modules/PRICE.sol:[136](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L136), [138](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L138), [222](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L222)

    ```solidity
    136            _movingAverage += (currentPrice - earliestPrice) / numObs;
    ...
    138            _movingAverage -= (earliestPrice - currentPrice) / numObs;
    ...
    222            total += startObservations_[i];
    ```

    - src/modules/VOTES.sol:[56](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L56), [58](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L58)

    ```solidity
    56        balanceOf[from_] -= amount_;
    ...
    58            balanceOf[to_] += amount_;
    ```

    - src/policies/Heart.sol:[103](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L103)

    ```solidity
    103        lastBeat += frequency();
    ```

12. **Deleting a struct is cheaper than creating a new struct with null values. (1 instances)**

    Deployment Gas Saved: **4 207**
    Method Call Gas Saved: **40**

    - src/policies/Governance.sol:[288](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L288)

    ```solidity
    288        activeProposal = ActivatedProposal(0, 0);
    ```

    fix:

    ```solidity
     delete activeProposal;
    ```

13. **Don't compare boolean expressions to boolean literals (2 instances)**

    Deployment Gas Saved: **1 607**

    - src/policies/Governance.sol:[223](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L223), [306](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L306)

    ```solidity
    223        if (proposalHasBeenActivated[proposalId_] == true) {
    ...
    306        if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {
    ```

14. **`revert` operator should be in the code as early as reasonably possible (3 instances)**

    Deployment Gas Saved: **200**
    Method Call Gas Saved: **1 559+**

    - src/modules/INSTR.sol:[43-48](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/INSTR.sol#L43-L48)

    ```solidity
    43        uint256 length = instructions_.length;
    44        uint256 instructionsId = ++totalInstructions;
    45
    46        Instruction[] storage instructions = storedInstructions[instructionsId];
    47
    48        if (length == 0) revert INSTR_InstructionsCannotBeEmpty(); // @note after 43
    ```

    - src/policies/Governance.sol:[180-191](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L180-L191), [241-249](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L241-L249)

    ```solidity
    180    function endorseProposal(uint256 proposalId_) external {
    181        uint256 userVotes = VOTES.balanceOf(msg.sender); // @note put after revert
    182
    183        if (proposalId_ == 0) {
    184            revert CannotEndorseNullProposal();
    185        }
    186
    187        Instruction[] memory instructions = INSTR.getInstructions(proposalId_);
    188        if (instructions.length == 0) {
    189            revert CannotEndorseInvalidProposal();
    190        }
    191
    ```

    ```solidity
    241        uint256 userVotes = VOTES.balanceOf(msg.sender); // @note put after revert
    242
    243        if (activeProposal.proposalId == 0) {
    244            revert NoActiveProposalDetected();
    245        }
    246
    247        if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
    248            revert UserAlreadyVoted();
    249        }
    ```

15. **Duplicated require()/revert() checks should be refactored to a modifier or function**

    Method Call Gas Saved: **8 111**

    - [src/modules/PRICE.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol)

    ```solidity
    if (!initialized) revert Price_NotInitialized(); // @note 4 instances
    ```
