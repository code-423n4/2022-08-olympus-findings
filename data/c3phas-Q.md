## QA

### Missing checks for address(0x0) when assigning values to address state variables

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L251

```solidity
File: /src/Kernel.sol
251:            executor = target_;

253:            admin = target_;
```

### constants should be defined rather than using magic numbers
There are several occurrences of literal values with unexplained meaning .Literal values in the codebase without an explained meaning make the code harder to read, understand and maintain, thus hindering the experience of developers, auditors and external contributors alike.

Developers should define a constant variable for every magic value used , giving it a clear and self-explanatory name. Additionally, for complex values, inline comments explaining how they were calculated or why they were chosen are highly recommended. Following [Solidity’s style guide](https://solidity.readthedocs.io/en/latest/style-guide.html#constants), constants should be named in UPPER_CASE_WITH_UNDERSCORES format, and specific public getters should be defined to read each one of them.

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L242-L250

```solidity
File: /src/modules/RANGE.sol
    function setSpreads(uint256 cushionSpread_, uint256 wallSpread_) external permissioned {
        // Confirm spreads are within allowed values
        if (
            wallSpread_ > 10000 ||  //@audit 1000
            wallSpread_ < 100 ||    //@audit 100
            cushionSpread_ > 10000 || //@audit 1000
            cushionSpread_ < 100 || //@audit 100
            cushionSpread_ > wallSpread_
        ) revert RANGE_InvalidParams();
		
		
264:         if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();		
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L90
```solidity
File: /src/modules/PRICE.sol
@audit: 38
90:        if (exponent > 38) revert Price_InvalidParams();

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L376-L379
```solidity
File: /src/policies/Operator.sol
376:            uint256 bondScale = 10 **  //@audit: 10
378:                uint8(
379:                    36 + scaleAdjustment + int8(reserveDecimals) - int8(ohmDecimals) - priceDecimals //@audit: 36
340:                );

430:            uint256 oracleScale = 10**uint8(int8(oracleDecimals) - priceDecimals); //@audit: 10
431:            uint256 bondScale = 10 ** //@audit: 10
432:                uint8(
433:                    36 + scaleAdjustment + int8(ohmDecimals) - int8(reserveDecimals) - priceDecimals //@audit: 36
434:                );

486:        while (price_ >= 10) { // @audit: 10
487:            price_ = price_ / 10;  // @audit: 10

518:        if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams(); //@audit: 10000 & 100

535:        if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams(); //@audit: 10_000

550:        if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams(); //@audit: 1000 & 100

753:                10**reserveDecimals * RANGE.price(true, false),  // @audit: 10
754:                10**ohmDecimals * 10**PRICE.decimals()  // @audit: 10

764:                10**ohmDecimals * 10**PRICE.decimals(), // @audit: 10
765:                10**reserveDecimals * RANGE.price(true, true) // @audit: 10

784:                    10**ohmDecimals * 10**PRICE.decimals(),  // @audit: 10
785:                    10**reserveDecimals * RANGE.price(true, true)  // @audit: 10
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L164
```solidity
File: /src/policies/Governance.sol
164:        if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT) // @audit: 10000

217:            (totalEndorsementsForProposal[proposalId_] * 100) < //@audit: 100

268:         if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) { //@audit: 100
```


### Non-assembly method available
```assembly { size := extcodesize() } => uint256 size = address().code.length``` We can minimize the complexity of the project by avoiding  using assembly where it's not necessary

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L31-L37
```solidity
File: /src/utils/KernelUtils.sol
31:   function ensureContract(address target_) view {
32:     uint256 size;
33:     assembly {
34:        size := extcodesize(target_)
35:    }
36:    if (size == 0) revert TargetNotAContract(target_);
37: }

```
### public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L451-L458
```solidity
File: /src/Kernel.sol
451:    function revokeRole(Role role_, address addr_) public onlyAdmin {
452:        if (!isRole[role_]) revert Kernel_RoleDoesNotExist(role_);
453:        if (!hasRole[addr_][role_]) revert Kernel_AddressDoesNotHaveRole(addr_, role_);

455:        hasRole[addr_][role_] = false;
456:
457:        emit RoleRevoked(role_, addr_);
458:    }

```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L451-L458

```solidity
File: /src/Kernel.sol
    function grantRole(Role role_, address addr_) public onlyAdmin {
        if (hasRole[addr_][role_]) revert Kernel_AddressAlreadyHasRole(addr_, role_);

        ensureValidRole(role_);
        if (!isRole[role_]) isRole[role_] = true;

        hasRole[addr_][role_] = true;

        emit RoleGranted(role_, addr_);
    }
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L75-L85

```solidity
File: /src/modules/TRSRY.sol
    function withdrawReserves(
        address to_,
        ERC20 token_,
        uint256 amount_
    ) public {
        _checkApproval(msg.sender, token_, amount_);

        token_.safeTransfer(to_, amount_);

        emit Withdrawal(msg.sender, to_, token_, amount_);
    }
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L215-L219
```solidity
File: /src/modules/RANGE.sol
    function updateMarket(
        bool high_,
        uint256 market_,
        uint256 marketCapacity_
    ) public permissioned {
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/INSTR.sol#L37
```solidity
File: /src/modules/INSTR.sol
37:    function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L145
```solidity
File: /src/policies/Governance.sol
145:    function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {

151:    function getActiveProposal() public view returns (ActivatedProposal memory) {

```

### Event is missing indexed fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

7 instances
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L20-L31

```solidity
File: /src/modules/RANGE.sol
20:    event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);

21:    event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);

22:    event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);

23:    event CushionDown(bool high_, uint256 timestamp_);

24:    event PricesChanged(
25:        uint256 wallLowPrice_,
26:        uint256 cushionLowPrice_,
27:        uint256 cushionHighPrice_,
28:        uint256 wallHighPrice_
29:    );

30:    event SpreadsChanged(uint256 cushionSpread_, uint256 wallSpread_);

31:    event ThresholdFactorChanged(uint256 thresholdFactor_);
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L26-L28
```solidity
File: /src/modules/PRICE.sol
26:    event NewObservation(uint256 timestamp_, uint256 price_, uint256 movingAverage_);

27:    event MovingAverageDurationChanged(uint48 movingAverageDuration_);

28:    event ObservationFrequencyChanged(uint48 observationFrequency_);
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L11
```solidity
File: /src/modules/INSTR.sol
11:    event InstructionsStored(uint256 instructionsId);
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L51-L54
```solidity
File: /src/policies/Operator.sol
51:    event CushionFactorChanged(uint32 cushionFactor_);

52:    event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);

53:    event ReserveFactorChanged(uint32 reserveFactor_);

54:    event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L28-L30
```solidity
File: /src/policies/Heart.sol
28:    event Beat(uint256 timestamp_);

29:    event RewardIssued(address to_, uint256 rewardAmount_);

30:    event RewardUpdated(ERC20 token_, uint256 rewardAmount_);
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L86-L90
```solidity
File: /src/policies/Governance.sol
86:    event ProposalSubmitted(uint256 proposalId);

87:    event ProposalEndorsed(uint256 proposalId, address voter, uint256 amount);

88:    event ProposalActivated(uint256 proposalId, uint256 timestamp);

89:    event WalletVoted(uint256 proposalId, address voter, bool for_, uint256 userVotes);

90:    event ProposalExecuted(uint256 proposalId);
```

### Unused named return
Using both named returns and a return statement isn’t necessary in  a function.To  improve code quality, consider using only one of those.

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L51-L53

```solidity
File: /src/modules/TRSRY.sol
51:    function VERSION() external pure override returns (uint8 major, uint8 minor) {
52:        return (1, 0);
53:    }

```
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L115-L117
```solidity
File: /src/modules/RANGE.sol
115:    function VERSION() external pure override returns (uint8 major, uint8 minor) {
116:        return (1, 0);
117:    }

```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L113-L115
```solidity
File: /src/modules/PRICE.sol
113:    function VERSION() external pure override returns (uint8 major, uint8 minor) {
114:        return (1, 0);
115:    }
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L27-L29
```solidity
File: /src/modules/VOTES.sol
27:    function VERSION() external pure override returns (uint8 major, uint8 minor) {
28:        return (1, 0);
29:    }
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/INSTR.sol#L28-L30
```solidity
File: /src/modules/INSTR.sol
28:    function VERSION() public pure override returns (uint8 major, uint8 minor) {
29:        return (1, 0);
30:    }
```

### Natspec is incomplete
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L234-L235
```solidity
File: /src/Kernel.sol

//@audit: Missing @param newKernel_
75:    /// @notice Function used by kernel when migrating to a new kernel.
76:    function changeKernel(Kernel newKernel_) external onlyKernel {

//@audit: Missing @param action_ , @param target
234:    /// @notice Main kernel function. Initiates state changes to kernel depending on Action passed in.
235:    function executeAction(Actions action_, address target_) external onlyExecutor {


//@audit: Missing @param role_, @param addr_
438:    /// @notice Function to grant policy-defined roles to some address. Can only be called by admin.
439:    function grantRole(Role role_, address addr_) public onlyAdmin {


//@audit: Missing @param role_, @param addr_
450:    /// @notice Function to revoke policy-defined roles from some address. Can only be called by admin.
451:    function revokeRole(Role role_, address addr_) public onlyAdmin {
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L63-L68
```solidity
File: /src/modules/TRSRY.sol

//@audit: Missing @param withdrawer_,@param token_ , @param amount_
63: /// @notice Sets approval for specific withdrawer addresses
64:    function setApprovalFor(
65:        address withdrawer_,
66:        ERC20 token_,
67:        uint256 amount_
68:    ) external permissioned {

//@audit: Missing @param token_, @param amount_
92:    function getLoan(ERC20 token_, uint256 amount_) external permissioned {

//@audit: Missing @param token_, @param amount_
105:    function repayLoan(ERC20 token_, uint256 amount_) external nonReentrant {


//@audit: Missing @param withdrawer_,@param token_ , @param amount_
122:    function setDebt(
123:        ERC20 token_,
124:        address debtor_,
125:        uint256 amount_
126:    ) external permissioned {
```


https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L41-L42
```solidity
File: /src/modules/INSTR.sol

//@audit: Missing @param instructions_, @param returns 
41:    /// @notice Store a list of Instructions to be executed in the future.
42:    function store(Instruction[] calldata instructions_) external permissioned returns (uint256) {
```

### Open todos
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L51

```solidity
File: /src/policies/TreasuryCustodian.sol

51:    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
52:    // TODO must reorg policy storage to be able to check for deactivated policies.

56:   // TODO Make sure `policy_` is an actual policy and not a random address.

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L657
```solidity
File: /src/policies/Operator.sol
657:        /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?

```

### Lack of event emission after critical initialize() functions

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L205
```solidity
File: /src/modules/PRICE.sol
205:     function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
```

### The nonReentrant modifier should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L272
```solidity
File: /src/policies/Operator.sol
    function swap(
        ERC20 tokenIn_,
        uint256 amountIn_,
        uint256 minAmountOut_
    ) external override onlyWhileActive nonReentrant returns (uint256 amountOut) {
```
