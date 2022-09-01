## FINDINGS

NB: *Some functions have been truncated where neccessary to just show affected parts of the code*
The gas estimates are the exact results from running the tests included with an exception of internal functions(we estimate based on number of SLOADS saved)
The optimizer is set to run with the default runs(200).
Throught the report some places might be denoted with audit tags to show the actual place affected.

### Using immutable on variables that are only set in the constructor and never after 
Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas)

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L28
```solidity
File: /src/policies/BondCallback.sol
28:    IBondAggregator public aggregator;

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L32
```solidity
File: /src/policies/BondCallback.sol
32:    ERC20 public ohm;

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L48
```solidity
File: /src/policies/Heart.sol
48:    IOperator internal _operator;
```


### The result of a function call should be cached rather than re-calling the function 

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L92-L109
### Heart.sol.beat(): frequency() should be cached(Saves ~351 gas)
```
Average Gas Before: 29228       Average Gas After: 28871
```

```solidity
File: /src/policies/Heart.sol
92:    function beat() external nonReentrant {
93:        if (!active) revert Heart_BeatStopped();
94:        if (block.timestamp < lastBeat + frequency()) revert Heart_OutOfCycle(); //@audit: frequency() 
            ...
102:        // Update the last beat timestamp
103:        lastBeat += frequency(); //@audit: frequency() 
            ...
109:    }
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L25-L35
### PriceConfig.sol.requestPermissions():PRICE.KEYCODE() should be cached( Saves ~1472 gas)
```
Average Gas Before: 3956       Average Gas After: 2484
```

```solidity
File: /src/policies/PriceConfig.sol
25:    function requestPermissions()
26:        external
27:        view
28:        override
29:        returns (Permissions[] memory permissions)
30:    {
31:        permissions = new Permissions[](3);
32:        permissions[0] = Permissions(PRICE.KEYCODE(), PRICE.initialize.selector); //@audit: PRICE.KEYCODE()
33:        permissions[1] = Permissions(PRICE.KEYCODE(), PRICE.changeMovingAverageDuration.selector);
34:        permissions[2] = Permissions(PRICE.KEYCODE(), PRICE.changeObservationFrequency.selector);
35:    }
```

**The above can be rewriten as follows:**
```solidity
    function requestPermissions()external view override returns (Permissions[] memory permissions){
	      Keycode PRICE_KEYCODE = PRICE.KEYCODE();
        permissions = new Permissions[](3);
        permissions[0] = Permissions(PRICE_KEYCODE, PRICE.initialize.selector); //@audit: PRICE.KEYCODE()
        permissions[1] = Permissions(PRICE_KEYCODE, PRICE.changeMovingAverageDuration.selector);
        permissions[2] = Permissions(PRICE_KEYCODE, PRICE.changeObservationFrequency.selector);
    }
```

**Other Instance:**
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/VoterRegistration.sol#L27-L36
### VoterRegistration.sol.requestPermissions():VOTES.KEYCODE() should be cached( Saves ~758 gas)
```
Average Gas Before: 2863       Average Gas After: 2105
```

```solidity
File: /src/policies/VoterRegistration.sol
27:    function requestPermissions()
28:        external
29:        view
30:        override
31:        returns (Permissions[] memory permissions)
32:    {
33:        permissions = new Permissions[](2);
34:        permissions[0] = Permissions(VOTES.KEYCODE(), VOTES.mintTo.selector);
35:        permissions[1] = Permissions(VOTES.KEYCODE(), VOTES.burnFrom.selector);
36:    }
```

See an existing implementation already on [Line 34](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L34-L40) for how to implement the above function
```solidity
    function requestPermissions() external view override returns (Permissions[] memory requests) {
        Keycode TRSRY_KEYCODE = TRSRY.KEYCODE();
		
        requests = new Permissions[](2);
        requests[0] = Permissions(TRSRY_KEYCODE, TRSRY.setApprovalFor.selector);
        requests[1] = Permissions(TRSRY_KEYCODE, TRSRY.setDebt.selector);
    }
```

### Use calldata instead of memory for function parameters
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.When arguments are read-only on external functions, the data location should be calldata:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L205
### PRICE.sol.initialize(): uint256\[] memory startObservations_ should be uint256[] calldata startObservations_(Saves ~ 1933 gas)
```
Average Gas Before: 432495       Average Gas After: 430562
```

```solidity
File: /src/modules/PRICE.sol
    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
        external
        permissioned
    {
        if (initialized) revert Price_AlreadyInitialized();

        // Cache numObservations to save gas.
        uint256 numObs = observations.length;

        // Check that the number of start observations matches the number expected
        if (startObservations_.length != numObs || lastObservationTime_ > uint48(block.timestamp))
            revert Price_InvalidParams();
        // Push start observations into storage and total up observations
        uint256 total;
        for (uint256 i; i < numObs; ) {
            if (startObservations_[i] == 0) revert Price_InvalidParams();
            total += startObservations_[i];
            observations[i] = startObservations_[i];
            unchecked {
                ++i;
            }
        }
        // Set moving average, last observation time, and initialized flag
        _movingAverage = total / numObs;
        lastObservationTime = lastObservationTime_;
        initialized = true;
    }
```

`startObservations_` should be declared calldata as it is readonly on this function

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L53-L67
### TreasuryCustodian.sol.revokePolicyApprovals(): ERC20\[] memory tokens_should be ERC20\[] calldata tokens_(Saves ~ 114 gas)
```
Average Gas Before: 6956       Average Gas After: 6842
```

```solidity
File: /src/policies/TreasuryCustodian.sol
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
`ERC20[] memory tokens_` should be declared as `ERC20[] calldata tokens_` as it is readonly in this function

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L152-L166
### BondCallback.sol.batchToTreasury(): ERC20\[] memory tokens_should be ERC20\[] calldata tokens_(Saves ~ 186 gas)
```
Average Gas Before: 12729       Average Gas After: 12543
```

```solidity
File: /src/policies/BondCallback.sol
    function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {
        ERC20 token;
        uint256 balance;
        uint256 len = tokens_.length;
        for (uint256 i; i < len; ) {
            token = tokens_[i];
            balance = token.balanceOf(address(this));
            token.safeTransfer(address(TRSRY), balance);
            priorBalances[token] = token.balanceOf(address(this));


            unchecked {
                ++i;
            }
        }
    }
```
`ERC20[] memory tokens_` should be declared as `ERC20[] calldata tokens_` as it is readonly in this function

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L45-L50
### PriceConfig.sol.initialize(): uint256[] memory startObservations_ should be uint256[] calldata startObservations_(Saves ~ 3580 gas)
```
Average Gas Before: 491657       Average Gas After: 488077
```

```solidity
File: /src/policies/PriceConfig.sol
45:    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
46:        external
47:        onlyRole("price_admin")
48:    {
49:        PRICE.initialize(startObservations_, lastObservationTime_);
50:    }

```
`uint256[] memory startObservations_` should be declared as `uint256[] calldata startObservations_` as it is readonly in this function


https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L159-L163
### Governance.sol.submitProposal(): string memory proposalURI_ should be string memory proposalURI_ (Saves ~ 3580 gas)
```
Average Gas Before: 491657       Average Gas After: 488077
```

```solidity
File: /src/policies/Governance.sol
159:    function submitProposal(
160:        Instruction[] calldata instructions_,
161:        bytes32 title_,
162:        string memory proposalURI_
163:    ) external {

```
`string memory proposalURI_` should be declared as `string calldata proposalURI_` as it is readonly in this function

### Caching storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L122-L147
### PRICE.sol.updateMovingAverage(): nextObsIndex should be cached(Saves ~123 gas)
```
Average Gas before:9124			Average Gas After: 9001
```
The gas saved ends up being higher than the estimates if we optimize the functions that are also called inside this one **(~259 gas)**
```solidity
File: /src/modules/PRICE.sol
122:    function updateMovingAverage() external permissioned {
            ...
129:        // Get earliest observation in window
130:        uint256 earliestPrice = observations[nextObsIndex];
            ...
141:        // Push new observation into storage and store timestamp taken at
142:        observations[nextObsIndex] = currentPrice;
143:        lastObservationTime = uint48(block.timestamp);
144:        nextObsIndex = (nextObsIndex + 1) % numObs;
            ...
147:    }
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L154-L180
### PRICE.sol.getCurrentPrice(): observationFrequency should be cached(Saves ~134 gas)
```
Average Gas before:5264			Average Gas After: 5130
```

```solidity
File: /src/modules/PRICE.sol
154:    function getCurrentPrice() public view returns (uint256) {
155:        if (!initialized) revert Price_NotInitialized();
		             ...
165:            if (updatedAt < block.timestamp - 3 * uint256(observationFrequency))
166:                revert Price_BadFeed(address(_ohmEthPriceFeed));
                 ...
171:            if (updatedAt < block.timestamp - uint256(observationFrequency))
172:                revert Price_BadFeed(address(_reserveEthPriceFeed));

```


https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L122-L147
### PRICE.sol.updateMovingAverage(): \_movingAverage should be cached(Saves 1 sload ~ 99 gas )
```
Average Gas before:9137			Average Gas After: 9038
```

```solidity
File: /src/modules/PRICE.sol
122:    function updateMovingAverage() external permissioned {
            ...
134:        // Calculate new moving average
135:        if (currentPrice > earliestPrice) {
136:            _movingAverage += (currentPrice - earliestPrice) / numObs;  //@audit: SLOAD 1 on happy path
137:        } else {
138:            _movingAverage -= (earliestPrice - currentPrice) / numObs;  //@audit: SLOAD 1 on sad path
139:        }
            ...
146:        emit NewObservation(block.timestamp, currentPrice, _movingAverage);//@audit: SLOAD 2
147:    }
```


https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L183-L187
### PRICE.sol.getLastPrice(): nextObsIndex should be cached(Saves 1 sload ~94 gas)

```solidity
File: /src/modules/PRICE.sol
183:    function getLastPrice() external view returns (uint256) {
184:        if (!initialized) revert Price_NotInitialized();
185:        uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;
186:        return observations[lastIndex];
187:    }

```

### PRICE.sol.changeMovingAverageDuration(): observationFrequency should be cached(Saves 1 sload)
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L240-L246
```solidity
File: /src/modules/PRICE.sol
240:    function changeMovingAverageDuration(uint48 movingAverageDuration_) external permissioned {
241:       // Moving Average Duration should be divisible by Observation Frequency to get a whole number of observations
242:        if (movingAverageDuration_ == 0 || movingAverageDuration_ % observationFrequency != 0) //@audit: SLOAD 1
243:            revert Price_InvalidParams();
            ...
245:        // Calculate the new number of observations
246:        uint256 newObservations = uint256(movingAverageDuration_ / observationFrequency); //@audit: SLOAD 2
```


### PRICE.sol.changeObservationFrequency(): movingAverageDuration should be cached(Saves 1 sload)
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L266-L272
```solidity
File: /src/modules/PRICE.sol
266:    function changeObservationFrequency(uint48 observationFrequency_) external permissioned {
267:       // Moving Average Duration should be divisible by Observation Frequency to get a whole number of observations
268:        if (observationFrequency_ == 0 || movingAverageDuration % observationFrequency_ != 0) //@audit: SLOAD 1
269:            revert Price_InvalidParams();
            ...
271:        // Calculate the new number of observations
272:        uint256 newObservations = uint256(movingAverageDuration / observationFrequency_); //@audit: SLOAD 2
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L92-L109
### Heart.sol.beat(): lastBeat should be cached(Saves 1 sload ~40gas , saves ~399 if we cache the result of the external call `frequency()` )

```
Estimations without caching the frequency function: only cache lastBeat
          Average Gas before:29228			Average Gas After: 29188

Estimations after caching the frequency function: lastBeat and frequency
          Average Gas before:29228			Average Gas After: 28829
```

```solidity
File: /src/policies/Heart.sol
92:    function beat() external nonReentrant {
93:        if (!active) revert Heart_BeatStopped();
94:        if (block.timestamp < lastBeat + frequency()) revert Heart_OutOfCycle(); //@audit: lastBeat SLOAD 1
            ...
102:        // Update the last beat timestamp
103:        lastBeat += frequency(); //@audit: lastBeat SLOAD 2
            ...
109:    }
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L111-L114
### Heart.sol.\_issueReward(): reward should be cached(Saves 1 sload ~94 gas)

```solidity
File: /src/policies/Heart.sol
111:    function _issueReward(address to_) internal {
112:        rewardToken.safeTransfer(to_, reward); //@audit: reward SLOAD 1
113:        emit RewardIssued(to_, reward); //@audit: reward SLOAD 2
114:    }
```

### Other interesting places that we can utilize caching.
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L240-L262
### Governance.sol.vote(): activeProposal.proposalId should be cached(Saves ~281 gas)
```
Average Gas Before: 61568       Average Gas After: 61287
```

```solidity
File: /src/policies/Governance.sol
240:    function vote(bool for_) external {
            ...
243:        if (activeProposal.proposalId == 0) { //@audit: activeProposal.proposalId
244:            revert NoActiveProposalDetected();
245:        }
            ...
247:        if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) { //@audit: activeProposal.proposalId
248:            revert UserAlreadyVoted();
249:        }
            ...
251:        if (for_) {
252:            yesVotesForProposal[activeProposal.proposalId] += userVotes; //@audit: activeProposal.proposalId
253:        } else {
254:            noVotesForProposal[activeProposal.proposalId] += userVotes; //@audit: activeProposal.proposalId
255:        }
		        ...
257:        userVotesForProposal[activeProposal.proposalId][msg.sender] = userVotes; //@audit: activeProposal.proposalId
            ...
261:        emit WalletVoted(activeProposal.proposalId, msg.sender, for_, userVotes);//@audit: activeProposal.proposalId
262:    }
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L265-L289
### Governance.sol.executeProposal(): activeProposal.proposalId should be cached(Saves ~147gas)
```
Average Gas Before: 171376       Average Gas After: 171229
```

```solidity
File: /src/policies/Governance.sol
265:    function executeProposal() external {
266:        uint256 netVotes = yesVotesForProposal[activeProposal.proposalId] -
267:            noVotesForProposal[activeProposal.proposalId];
            ...    
276:        Instruction[] memory instructions = INSTR.getInstructions(activeProposal.proposalId);
            ...
285:        emit ProposalExecuted(activeProposal.proposalId);
            ...
289:    }
```

## Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L

```solidity
File: /src/Kernel.sol
279:    function _upgradeModule(Module newModule_) internal {

295:    function _activatePolicy(Policy policy_) internal {

325:    function _deactivatePolicy(Policy policy_) internal {

351:    function _migrateKernel(Kernel newKernel_) internal {

409:    function _pruneFromDependents(Policy policy_) internal {

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L111-L114
```solidity
File: /src/policies/Heart.sol
111:    function _issueReward(address to_) internal {
112:        rewardToken.safeTransfer(to_, reward);
113:        emit RewardIssued(to_, reward);
114:    }
```
The above function is only called on [Line 106](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L106)

### Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage somestruct = someMap[someIndex]``` and use it.

### TRSRY.sol.repayLoan(): reserveDebt\[token_]\[msg.sender] should be cached
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L105-L119

```solidity
File: /src/modules/TRSRY.sol
105:    function repayLoan(ERC20 token_, uint256 amount_) external nonReentrant {
106:        if (reserveDebt[token_][msg.sender] == 0) revert TRSRY_NoDebtOutstanding(); 

108:        // Deposit from caller first (to handle nonstandard token transfers)
109:        uint256 prevBalance = token_.balanceOf(address(this));
110:        token_.safeTransferFrom(msg.sender, address(this), amount_);

112:        uint256 received = token_.balanceOf(address(this)) - prevBalance;

114:        // Subtract debt from caller
115:        reserveDebt[token_][msg.sender] -= received; 
116:        totalDebt[token_] -= received;

118:        emit DebtRepaid(token_, msg.sender, received);
119:    }
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L205-L236
### Governance.sol.activateProposal(): proposalHasBeenActivated\[proposalId_]
```solidity
File: /src/policies/Governance.sol
205:    function activateProposal(uint256 proposalId_) external {
206:        ProposalMetadata memory proposal = getProposalMetadata[proposalId_];

223:        if (proposalHasBeenActivated[proposalId_] == true) { 
224:            revert ProposalAlreadyActivated();
225:        }

233:        proposalHasBeenActivated[proposalId_] = true; 

236:    }
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L295-L314
### Governance.sol.reclaimVotes():tokenClaimsForProposal\[proposalId_]\[msg.sender] should be cached
```solidity
File: /src/policies/Governance.sol
295:    function reclaimVotes(uint256 proposalId_) external {
 
306:        if (tokenClaimsForProposal[proposalId_][msg.sender] == true) { 
307:            revert VotingTokensAlreadyReclaimed();
308:        }

310:        tokenClaimsForProposal[proposalId_][msg.sender] = true;

313:    }
314: }
```


### Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L36-L39
```solidity
File: /src/modules/TRSRY.sol
36:    mapping(ERC20 => uint256) public totalDebt;

38:    /// @notice Debt for particular token and debtor address
39:    mapping(ERC20 => mapping(address => uint256)) public reserveDebt;
```

## Use Shift Right/Left instead of Division/Multiplication
While the DIV / MUL opcode uses 5 gas, the SHR / SHL opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated, so the calculation can be unchecked in Solidity version 0.8+

[relevant source](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L372
```solidity
File: /src/policies/Operator.sol
372:            int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);

427:            int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
```


### x += y costs more gas than x = x + y for state variables
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L103
```solidity
File: /src/policies/Heart.sol
103:        lastBeat += frequency();
```
The above should be modified to 
```solidity
103:        lastBeat = lastBeat + frequency();
```

### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

   When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L205
```solidity
File: /src/modules/PRICE.sol
//@audit: uint48 lastObservationTime_
205:    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_) 

//@audit: uint48 movingAverageDuration_
240:    function changeMovingAverageDuration(uint48 movingAverageDuration_) external permissioned {

//@audit: uint48 observationFrequency_
266:    function changeObservationFrequency(uint48 observationFrequency_) external permissioned {

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L527-L531
```solidity
File: /src/policies/Operator.sol
527:    function setCushionParams(
528:        uint32 duration_,
529:        uint32 debtBuffer_,
530:        uint32 depositInterval_
531:    ) external onlyRole("operator_policy") {

548:     function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {

559:    function setRegenParams(
560:        uint32 wait_,
561:        uint32 threshold_,
562:        uint32 observe_
563:    ) external onlyRole("operator_policy") {
	
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L45
```solidity
File: /src/policies/PriceConfig.sol
//@audit: uint48 lastObservationTime_
45:    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)

//@audit: uint48 movingAverageDuration_
58:    function changeMovingAverageDuration(uint48 movingAverageDuration_)

//@audit: uint48 observationFrequency_
69:    function changeObservationFrequency(uint48 observationFrequency_)
```

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L136
```solidity
136:            _movingAverage += (currentPrice - earliestPrice) / numObs;
```
The operation `currentPrice - earliestPrice` cannot underflow due to the check on [Line 135](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L135) which ensures that `currentPrice` is greater than `earliestPrice`
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L138
```solidity
138:             _movingAverage -= (earliestPrice - currentPrice) / numObs;
```
The operation `earliestPrice - currentPrice` cannot underflow due to the check on [Line 135](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L135) which ensures that this operation would only be perfomened if `earliestPrice` is greter than `currentPrice`

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L185
```solidity
185:        uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;

```

The operation ` nextObsIndex - 1` cannot underflow as it would only be perfomed if `nextObsIndex` is not equal to 0. As `nextObsIndex` is a uint if it's not equal to 0 then it must be greater than 0

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L131
```solidity
131:        if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;
132:        else totalDebt[token_] -= oldDebt - amount_;
```
The operation  `amount_ - oldDebt` cannot underlow due the check `if (oldDebt < amount_)` that ensures that amount is greater than oldDebt before performng the operation
The operation  `oldDebt - amount_` cannot underlow due the check `if (oldDebt < amount_)` that ensures that this operation would only be perfomed if `oldDebt ` is greater than `amount_`


### Cache the length of arrays in loops (saves ~6 gas per iteration)
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

The solidity compiler will always read the length of the array during each iteration. That is,

   1.if it is a storage array, this is an extra sload operation (100 additional extra gas (EIP-2929 2) for each iteration except for the first),
   2.if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first),
   3.if it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)

This extra costs can be avoided by caching the array length (in stack):
 When reading the length of an array,  **sload** or **mload** or **calldataload** operation is only called once and subsequently replaced by a cheap **dupN** instruction. Even though mload , calldataload and dupN have the same gas cost, mload and calldataload needs an additional dupN to put the offset in the stack, i.e., an extra 3 gas. which brings this to 6 gas
 
Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead:

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L276-L278
```solidity
File: /src/policies/Governance.sol
276:        Instruction[] memory instructions = INSTR.getInstructions(activeProposal.proposalId);

278:        for (uint256 step; step < instructions.length; ) {
```

  **The above should be modified to**
```solidity

276:        Instruction[] memory instructions = INSTR.getInstructions(activeProposal.proposalId);
277:        uint256 length = instructions.length;
278:        for (uint256 step; step < length; ) {
```

### ++i costs less gas compared to i++ or i += 1  (~5 gas per iteration)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:

```solidity
uint i = 1;  
i++; // == 1 but i == 2  
```

But ++i returns the actual incremented value:

```solidity
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

Instances include:

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L49

```solidity
File: /src/utils/KernelUtils.sol

49:            i++;

64:            i++;

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L488
```solidity
File: /src/policies/Operator.sol
488:            decimals++;

```

### Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.
I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here:

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L223
```solidity
File: /src/policies/Governance.sol
223:        if (proposalHasBeenActivated[proposalId_] == true) {

306:        if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {
```

### Using bools for storage incurs overhead
 Booleans are more expensive than uint256 or any type that takes up a full  word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

**Instances affected include**

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L113

```solidity
File: /src/Kernel.sol
113:     bool public isActive;

181:     mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions;

194:     mapping(address => mapping(Role => bool)) public hasRole;

197:     mapping(Role => bool) public isRole;

```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L62
```solidity
File: /src/modules/PRICE.sol
62:    bool public initialized;

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L63
```solidity
File: /src/policies/Operator.sol
63:    bool public initialized;

66:    bool public active;

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L24
```solidity
File: /src/policies/BondCallback.sol
24:    mapping(address => mapping(uint256 => bool)) public approvedMarkets;

```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L33
```solidity
File: /src/policies/Heart.sol
33:    bool public active;
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L105
```solidity
File: /src/policies/Governance.sol
105:    mapping(uint256 => bool) public proposalHasBeenActivated;

117:    mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;
```

### Using private rather than public for constants, saves gas
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L121-L137
```solidity
File: /src/policies/Governance.sol
121:    uint256 public constant SUBMISSION_REQUIREMENT = 100;

124:    uint256 public constant ACTIVATION_DEADLINE = 2 weeks;

127:    uint256 public constant GRACE_PERIOD = 1 weeks;

130:    uint256 public constant ENDORSEMENT_THRESHOLD = 20;

133:    uint256 public constant EXECUTION_THRESHOLD = 33;

137:    uint256 public constant EXECUTION_TIMELOCK = 3 days;
```

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L89
```solidity
File: /src/policies/Operator.sol
89:    uint32 public constant FACTOR_SCALE = 1e4;
```

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L65
```solidity
File: /src/modules/RANGE.sol
65:    uint256 public constant FACTOR_SCALE = 1e4;
```
