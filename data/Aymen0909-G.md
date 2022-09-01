# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Multiple mappings related to the same data can be combined into a single mapping using a struct   | 8      |
| 2      | State variables only set in the constructor should be declared `immutable`     | 1 |
| 3      | Variables inside `struct` should be packed to save gas     | 1    |
| 4      | Using `bool` for storage incurs overhead   |  5 |
| 5      | Usage of `uints/ints` smaller than 32 bytes (256 bits) incurs overhead  |  5 |
| 6      | `array.length` should not be looked up in every loop in a for loop |  1 |
| 7      | Use `calldata` instead of `memory` for function parameters type  |  2 |
| 8      | `X += Y/X -= Y` costs more gas than `X = X + Y/X = X - Y` for state variables  |  14 |
| 9      | It costs more gas to initialize variables to zero than to let the default of zero be applied    |  4  |
| 10      | Use of `++i` cost less gas than `i++` in for loops    |  2  |
| 11      | Using `private` rather than `public` for constants, saves gas |  7 |
| 12      | Functions guaranteed to revert when called by normal users can be marked `payable` |  30 |

## Findings

### 1- Multiple mappings related to the same data can be combined into a single mapping using a struct :

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 8 instances of this issue :

```
File: src/policies/Governance.sol

96      mapping(uint256 => ProposalMetadata) public getProposalMetadata;
99      mapping(uint256 => uint256) public totalEndorsementsForProposal;
102     mapping(uint256 => mapping(address => uint256)) public userEndorsementsForProposal;
105     mapping(uint256 => bool) public proposalHasBeenActivated;
108     mapping(uint256 => uint256) public yesVotesForProposal;
111     mapping(uint256 => uint256) public noVotesForProposal;
114     mapping(uint256 => mapping(address => uint256)) public userVotesForProposal;
117     mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;
```

These mappings could be refactored into the following `struct` and `mapping` for example :

```
struct Proposal {
        ProposalMetadata metadata;
        uint256 totalEndorsementsForProposal;
        uint256 yesVotesForProposal;
        uint256 noVotesForProposal;
        bool proposalHasBeenActivated;
        mapping(address => uint256) userEndorsementsForProposal;
        mapping(address => uint256) userVotesForProposal;
        mapping(address => bool) tokenClaimsForProposal;
    }
    
mapping(uint256 => Proposal) public proposals;
```

### 2- State variables only set in the constructor should be declared `immutable` :

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

There is 1 instance of this issue:

```
File: src/policies/BondCallback.sol

44      ohm = ohm_;
```

### 3- Variables inside `struct` should be packed to save gas :

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas.

There is 1 instance of this issue:

```
File: src/policies/Governance.sol

44      struct ActivatedProposal {
            uint256 proposalId;
            uint256 activationTimestamp;
        }
```

Because none of the variables inside the ActivatedProposal `struct` can overflow 2**128, it should be rearranged as follow :

```
struct ProposalTemp {
    uint128 proposalId;
    uint128 activationTimestamp;
}
```

### 4- Using `bool` for storage incurs overhead  :
 
Use `uint256(1)` and `uint256(2)` instead of `true/false` to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from `false` to `true`, after having been `true` in the past

There are 5 instances of this issue:

```
File: src/Kernel.sol

113     bool public isActive;

File: src/modules/PRICE.sol

62      bool public initialized;

File: src/policies/Heart.sol

33      bool public active;

File: src/modules/Operator.sol

63      bool public initialized;
66      bool public active;

```

### 5- Usage of `uints/ints` smaller than 32 bytes (256 bits) incurs overhead :

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size as you can check [here](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html).

So use `uint256`/`int256` for state variables and then downcast to lower sizes where needed.

There are 5 instances of this issue:

```
File: src/modules/PRICE.sol

44      uint32 public nextObsIndex;
47      uint32 public numObservations;
50      uint48 public observationFrequency;
53      uint48 public movingAverageDuration;
56      uint48 public lastObservationTime;
```

### 6- `array.length` should not be looked up in every loop in a for loop :

The overheads outlined below are PER LOOP, excluding the first loop :

* storage arrays incur a Gwarmaccess (100 gas)
* memory arrays use MLOAD (3 gas)
* calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

There is 1 instance of this issue:

```
File: src/policies/Governance.sol

278     for (uint256 step; step < instructions.length; )
```

### 7- Use `calldata` instead of `memory` for function parameters type :

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

There are 2 instances of this issue:

```
File: src/policies/BondCallback.sol

164     function batchToTreasury(ERC20[] memory tokens_) external

File: src/policies/TreasuryCustodian.sol

53      function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external
```

### 8- `X += Y/X -= Y` costs more gas than `X = X + Y/X = X - Y` for state variables :

There are 14 instances of this issue:

```
File: src/policies/Governance.sol

194     totalEndorsementsForProposal[proposalId_] -= previousEndorsement;
198     totalEndorsementsForProposal[proposalId_] += userVotes;
252     yesVotesForProposal[activeProposal.proposalId] += userVotes;
254     noVotesForProposal[activeProposal.proposalId] += userVotes;

File: src/modules/VOTES.sol

56      balanceOf[from_] -= amount_;
198     balanceOf[to_] += amount_;

File: src/modules/TRSRY.sol

96      reserveDebt[token_][msg.sender] += amount_;
97      totalDebt[token_] += amount_;
115     reserveDebt[token_][msg.sender] -= received;
116     totalDebt[token_] -= received;
131     if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;
132     else totalDebt[token_] -= oldDebt - amount_;

File: src/modules/PRICE.sol

136     _movingAverage += (currentPrice - earliestPrice) / numObs;
138     _movingAverage -= (earliestPrice - currentPrice) / numObs;
```

### 9- It costs more gas to initialize variables to zero than to let the default of zero be applied :

If a variable is not set/initialized, it is assumed to have the default value (0 for uint or int, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 3 instances of this issue:

```
File: src/Kernel.sol

397     for (uint256 i = 0; i < reqLength; )

File: src/utils/KernelUtils.sol

43      for (uint256 i = 0; i < 5; )
58      for (uint256 i = 0; i < 32; )

```

### 10- Use of ++i cost less gas than i++ in for loops :

There are 2 instances of this issue:

```
File: src/utils/KernelUtils.sol

49     i++;
64     i++;
```


### 11- Using private rather than public for constants, saves gas :

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

There are 7 instances of this issue:

```
File: src/policies/Governance.sol

121      uint256 public constant SUBMISSION_REQUIREMENT = 100;
124      uint256 public constant ACTIVATION_DEADLINE = 2 weeks;
127      uint256 public constant GRACE_PERIOD = 1 weeks;
130      uint256 public constant ENDORSEMENT_THRESHOLD = 20;
133      uint256 public constant EXECUTION_THRESHOLD = 33;
137      uint256 public constant EXECUTION_TIMELOCK = 3 days;

File: src/policies/Operator.sol

89       uint32 public constant FACTOR_SCALE = 1e4;
```

### 12- Functions guaranteed to revert when called by normal users can be marked `payable` :

If a function modifier such as `onlyAdmin` or `onlyRole` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 30 instances of this issue:

```
File: src/Kernel.sol

235     function executeAction(Actions action_, address target_) external onlyExecutor
439     function grantRole(Role role_, address addr_) public onlyAdmin
451     function revokeRole(Role role_, address addr_) public onlyAdmin
439     function grantRole(Role role_, address addr_) public onlyAdmin

File: src/policies/BondCallback.sol

90      function whitelist(address teller_, uint256 id_) external override onlyRole("callback_whitelist")
164     function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin")
205     function setOperator(Operator operator_) external onlyRole("callback_admin")

File: src/policies/Heart.sol

137     function resetBeat() external onlyRole("heart_admin")
142     function toggleBeat() external onlyRole("heart_admin")
147     function setRewardTokenAndAmount(ERC20 token_, uint256 reward_) external onlyRole("heart_admin")
157     function withdrawUnspentRewards(ERC20 token_) external onlyRole("heart_admin")

File: src/policies/Operator.sol

346     function bondPurchase(uint256 id_, uint256 amountOut_) external onlyWhileActive onlyRole("operator_reporter")
498     function setSpreads(uint256 cushionSpread_, uint256 wallSpread_) external onlyRole("operator_policy")
510     function setThresholdFactor(uint256 thresholdFactor_) external onlyRole("operator_policy")
516     function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy")
527     function setCushionParams(
            uint32 duration_,
            uint32 debtBuffer_,
            uint32 depositInterval_
        ) external onlyRole("operator_policy")
548     function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy")
559     function setRegenParams(
            uint32 wait_,
            uint32 threshold_,
            uint32 observe_
        ) external onlyRole("operator_policy")
586     function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_) external onlyRole("operator_admin")
598     function initialize() external onlyRole("operator_admin") 
618     function regenerate(bool high_) external onlyRol("operator_admin")
624     function toggleActive() external onlyRole("operator_admin")

File: src/policies/PriceConfig.sol

45      function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_) external onlyRole("price_admin")
58      function changeMovingAverageDuration(uint48 movingAverageDuration_) external onlyRole("price_admin")
69      function changeObservationFrequency(uint48 observationFrequency_) external onlyRole("price_admin")

File: src/policies/TreasuryCustodian.sol

42      function grantApproval(
            address for_,
            ERC20 token_,
            uint256 amount_
        ) external onlyRole("custodian")
71      function increaseDebt(
            ERC20 token_,
            address debtor_,
            uint256 amount_
        ) external onlyRole("custodian")
80      function decreaseDebt(
            ERC20 token_,
            address debtor_,
            uint256 amount_
        ) external onlyRole("custodian")
        
File: src/policies/VoterRegistration.sol

45      function issueVotesTo(address wallet_, uint256 amount_) external onlyRole("voter_admin")
53      function revokeVotesFrom(address wallet_, uint256 amount_) external onlyRole("voter_admin")
```