# QA Report for Olympus DAO contest

## Overview
During the audit, 6 low and 13 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | [Storage variables can be packed tightly](#l-1-storage-variables-can-be-packed-tightly) | Low | 1
L-2 | [Missing check](#l-2-missing-check) | Low | 1
L-3 | [Large number of observations may cause out-of-gas error](#l-3-large-number-of-observations-may-cause-out-of-gas-error) | Low | 1
L-4 | [Incorrect code or comment](#l-4-incorrect-code-or-comment) | Low | 1
L-5 | [Misleading comments](#l-5-misleading-comments) | Low | 4
L-6 | [Check zero denominator](#l-6-check-zero-denominator) | Low | 2
NC-1 | [Constructor before modifiers](#nc-1-constructor-before-modifiers) | Non-Critical | 6
NC-2 | [Events before state variables](#nc-2-events-before-state-variables) | Non-Critical | 8
NC-3 | [State variables after constructor](#nc-3-state-variables-after-constructor) | Non-Critical | 1
NC-4 | [Public functions before external](#nc-4-public-functions-before-external) | Non-Critical | 13
NC-5 | [Сomment in the wrong place](#nc-5-comment-in-the-wrong-place) | Non-Critical | 1
NC-6 | [EVENTS, STATE VARIABLES, ERRORS comments not everywhere](#nc-6-events-state-variables-errors-comments-not-everywhere) | Non-Critical | 22
NC-7 | [No function visibility](#nc-7-no-function-visibility) | Non-Critical | 7
NC-8 | [Typo](#nc-8-typo) | Non-Critical | 1
NC-9 | [Scientific notation may be used](#nc-9-scientific-notation-may-be-used) | Non-Critical | 7
NC-10 | [Constants may be used](#nc-10-constants-may-be-used) | Non-Critical | 16
NC-11 | [Open TODOs](#nc-11-open-todos) | Non-Critical | 4
NC-12 | [Missing NatSpec](#nc-12-missing-natspec) | Non-Critical | 32
NC-13 | [Public functions can be external](#nc-13-public-functions-can-be-external) | Non-Critical | 7

## Low Risk Findings (6)
### L-1. Storage variables can be packed tightly
##### Description
According to [docs](https://docs.soliditylang.org/en/v0.8.16/internals/layout_in_storage.html#layout-of-state-variables-in-storage), multiple, contiguous items that need less than 32 bytes are packed into a single storage slot if possible. It might be beneficial to use reduced-size types if you are dealing with storage values because the compiler will pack multiple elements into one storage slot, and thus, combine multiple reads or writes into a single operation. 

##### Instances
[Link to code](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L80)
```
/// Tokens
/// @notice     OHM token contract
ERC20 public immutable ohm;
uint8 public immutable ohmDecimals;
/// @notice     Reserve token contract
ERC20 public immutable reserve;
uint8 public immutable reserveDecimals;

 /// Constants
uint32 public constant FACTOR_SCALE = 1e4;
```
##### Recommendation
Consider changing order of variables to:
```
/// Tokens
/// @notice     OHM token contract
ERC20 public immutable ohm;
/// @notice     Reserve token contract
ERC20 public immutable reserve;

//PACKED
uint8 public immutable ohmDecimals;
uint8 public immutable reserveDecimals;

/// Constants
uint32 public constant FACTOR_SCALE = 1e4;
```

#
### L-2. Missing check
##### Description
No check that observationFrequency not equal to zero (like [here](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L268)). Without check, constructor call will fail not indicating Price_InvalidParams() error.

##### Instances
[```if (movingAverageDuration_ == 0 || movingAverageDuration_ % observationFrequency_ != 0)```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L79)
##### Recommendation
Change to:  
```if (observationFrequency_ == 0 || movingAverageDuration_ == 0 || movingAverageDuration_ % observationFrequency_ != 0)```

#
### L-3. Large number of observations may cause out-of-gas error
##### Description
[Loops](https://docs.soliditylang.org/en/develop/security-considerations.html#gas-limit-and-loops) that do not have a fixed number of iterations, for example, loops that depend on storage values, have to be used carefully: Due to the block gas limit, transactions can only consume a certain amount of gas. Either explicitly or just due to normal operation, the number of iterations in a loop can grow beyond the block gas limit, which can cause the complete contract to be stalled at a certain point. 

##### Instances
[Link to code](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L220)
```
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
```

##### Recommendation
Restrict the maximum number of observations.

#
### L-4. Incorrect code or comment
##### Description
Comment says that the variable ```numObservations``` should be cached but there is a call to the length of the ```observations``` array.

##### Instances
[Link to code](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L212):
```
// Cache numObservations to save gas.
uint256 numObs = observations.length;
```

##### Recommendation
Consider changing the code or comment. The contract has similar part of the [code](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L126-L127), so it seems preferable to change the code to:
```
// Cache number of observations to save gas.
uint32 numObs = numObservations;
```
The code will become more consistent.

#
### L-5. Misleading comments
##### Description
After ```/* ========== INTERNAL FUNCTIONS ========== */``` comment, not all internal functions are placed. There are other internal functions in the contract, and they are located in other places in the code.
The same with ```/* ========== VIEW FUNCTIONS ========== */``` comment.

##### Instances
- [Operator.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol) (internal)
- [Operator.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol) (view)
- [BondCallback.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol) (view)
- [interfaces/IOperator.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol) (view)

##### Recommendation
Regroup functions or formulate more specific comments.

#
### L-6. Check zero denominator
##### Description
[```range.cushion.low.price```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L419) and [```range.wall.low.price```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L420) may be equal to zero, since they are initialized to zero [here](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L82).

##### Recommendation
Add the check to prevent function call failure.

## Non-Critical Risk Findings (13)
### NC-1. Constructor before modifiers
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), modifiers should be declared before constructor.

##### Instances
- [Kernel.sol: 70](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L70)
- [Kernel.sol: 88](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L88)
- [Kernel.sol: 119](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L119)
- [Kernel.sol: 223](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L223)
- [Kernel.sol: 229](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L229)
- [Operator.sol: 188](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L188)

##### Recommendation
Place modifiers before constructor.

#
### NC-2. Events before state variables
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), state variables should be declared before events.

##### Instances
- [TRSRY.sol: 20](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L20) - [ 29](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L29)
- [RANGE.sol: 20](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L20) - [31](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L31)
- [PRICE.sol: 26](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L26) - [28](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L28)
- [INSTR.sol: 11](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L11) 
- [TreasuryCustodian.sol: 17](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L17) 
- [Operator.sol: 44](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L44) - [54](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L54)
- [Heart.sol: 28](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L28) - [30](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L30)
- [Governance.sol: 86](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L86) - [90](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L90)

##### Recommendation
Place state variables before events.

#
### NC-3. State variables after constructor
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), constructor should be declared before state variables.

##### Instances
[Governance.sol: 93](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L93) - [137](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L137)

##### Recommendation
Place constructor before state variables.

#
### NC-4. Public functions before external
##### Description
According to [Order of Functions](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), external functions should be declared before public.

##### Instances
- [Kernel.sol: 95](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L95)
- [TRSRY.sol: 47](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L47)
- [MINTR.sol: 20](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/MINTR.sol#L20)
- [RANGE.sol: 110](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L110)
- [RANGE.sol: 215](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L215)
- [PRICE.sol: 108](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L108)
- [PRICE.sol: 154](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L154)
- [VOTES.sol: 22](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L22)
- [INSTR.sol: 37](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L37)
- [Operator.sol: 749](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L749)
- [Operator.sol: 778](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L778)
- [Governance.sol: 145](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L145)
- [Governance.sol: 151](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L151)

##### Recommendation
Place external functions before public.

#
### NC-5. Comment in the wrong place
##### Description
Messed up comments.

##### Instances
[Link](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L16)
```
    /* ========== STATE VARIABLES ========== */
    event ApprovalRevoked(address indexed policy_, ERC20[] tokens_);

    // Modules
    OlympusTreasury internal TRSRY;
```

##### Recommendation
Change to:
```
    /* ========== EVENTS =========== */
    event ApprovalRevoked(address indexed policy_, ERC20[] tokens_);

    /* ========== STATE VARIABLES ========== */
    // Modules
    OlympusTreasury internal TRSRY;
```

#
### NC-6. EVENTS, STATE VARIABLES, ERRORS comments not everywhere
##### Description
Some contracts (for example, [this](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L25)) have ```/* ========== EVENTS =========== */``` and ```/* ========== STATE VARIABLES ========== */``` comments and some do not. Only one [contract](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L34) has ```/* ========== ERRORS =========== */``` comment, although others also declares errors.

##### Instances
Contracts without ```/* ========== EVENTS =========== */```  and ```/* ========== STATE VARIABLES ========== */```  comments:
- [TRSRY.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol)
- [RANGE.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol)
- [INSTR.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol)
- [Heart.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol)
- [Governance.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol)  

Contracts without ```/* ========== ERRORS =========== */```  comment:
- All 17, [except this](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol).

##### Recommendation
Add missing comments.

#
### NC-7. No function visibility
##### Description
No visibility specified.

##### Instances
All 7 functions  in [KernelUtils.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol)

##### Recommendation
Declare functions visibility.

#
### NC-8. Typo
##### Description
Typo in comment.

##### Instances
[```// Cache numbe of observations to save gas.```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L126)

##### Recommendation
Change to:  
```// Cache number of observations to save gas.```

#
### NC-9. Scientific notation may be used
##### Description
For readability, it is better to use scientific notation.

##### Instances
- [```wallSpread_ > 10000 ||```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L245)
- [```cushionSpread_ > 10000 ||```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L247)
- [```if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L264)
- [```if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L111)
- [```if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L518)
- [```if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L550)
- [```if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L164)

##### Recommendation
Replace, for example, ```10000``` with ```10e4```.

#
### NC-10. Constants may be used
##### Description
Constants may be used instead of  literal values.

##### Instances
- [```for (uint256 i = 0; i < 5; ) ```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L43) (for 5)
- [```for (uint256 i = 0; i < 32; )```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L58) (for 32)
- [```wallSpread_ < 100  ||```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L246) (for 100)
- [```cushionSpread_ < 100 ||```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L248) (for 100)
- [```wallSpread_ > 10000 ||```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L245) (for 10000)
- [```cushionSpread_ > 10000 ||```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L247) (for 10000)
- [```if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) ```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L264) (for 100 and 10000)
- [```if (exponent > 38) revert Price_InvalidParams();```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L90) (for 38)
- [```if (configParams[2] < uint32(10_000))```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L106) (for 10_000)
- [```if (configParams[4] > 10000 || configParams[4] < 100)```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L111) (for 100 and 10000)
- [```if (cushionFactor_ > 10000 || cushionFactor_ < 100)```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L518) (for 100 and 10000)
- [```if (debtBuffer_ < uint32(10_000))```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L535) (for 10_000)
- [```if (reserveFactor_ > 10000 || reserveFactor_ < 100)```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L550) (for 100 and 10000)
- [```if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L164) (for 10000)
- [```(totalEndorsementsForProposal[proposalId_] * 100)```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L217) (for 100)
- [```if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD)```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L268) (for 100)

##### Recommendation
Define constant variables, especially, for repeated values.

#
### NC-11. Open TODOs
##### Description
[Operator.sol](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol) has 1 open TODO, [TreasuryCustodian.sol](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol) has 3 open TODO. 

##### Instances
- [```/// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
/// Current price is guaranteed to be up to date, but may be a bad value if not checked?```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L657)
- [```// TODO Currently allows anyone to revoke any approval EXCEPT activated policies.```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L51)
- [```// TODO must reorg policy storage to be able to check for deactivated policies.```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L52)
- [```// TODO Make sure `policy_` is an actual policy and not a random address.```](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L56)

##### Recommendation
Resolve issues.

#
### NC-12. Missing NatSpec
##### Description
NatSpec is missing for 32 functions in 9 contracts.

##### Instances
- [7 functions in Kernel.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol)
- [7 functions in KernelUtils.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol)
- [4 functions in TRSRY.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol)
- [2 functions in MINTR.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol)
- [2 functions in VOTES.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol)
- [5 functions in TreasuryCustodian.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol)
- [1 function in Heart.sol](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L111)
- [2 functions in PriceConfig.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol)
- [2 functions in Governance.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol)

##### Recommendation
Add NatSpec for all functions.

#
### NC-13. Public functions can be external
##### Description
If functions are not called by the contract where they are defined, they can be declared external.

##### Instances
All 7 functions from [KernelUtils.sol](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol).

##### Recommendation
Make public functions external, where possible.