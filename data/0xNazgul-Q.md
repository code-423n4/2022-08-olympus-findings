## [NAZ-L1] Missing Time locks
**Severity**: Low 
**Context**: [`RANGE.sol#L263`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L263), [`PRICE.sol#L240`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L240), [`PRICE.sol#L266`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L266), [`Operator.sol#L516`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L516), [`Operator.sol#L527`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L527), [`Operator.sol#L548`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L548), [`Operator.sol#L559`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L559), [`Operator.sol#L586`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L586), [`Heart.sol#L130`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L130), [`Heart.sol#L135`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L135), [`Heart.sol#L140`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L140)

**Description**:
When critical parameters of systems need to be changed, it is required to broadcast the change via event emission and recommended to enforce the changes after a time-delay. This is to allow system users to be aware of such critical changes and give them an opportunity to exit or adjust their engagement with the system accordingly. None of the onlyOwner functions that change critical protocol addresses/parameters have a timelock for a time-delayed change to alert: (1) users and give them a chance to engage/exit protocol if they are not agreeable to the changes (2) team in case of compromised owner(s) and give them a chance to perform incident response.

**Recommendation**:
Users may be surprised when critical parameters are changed. Furthermore, it can erode users' trust since they can’t be sure the protocol rules won’t be changed later on. Compromised owner keys may be used to change protocol addresses/parameters to benefit attackers. Without a time-delay, authorized owners have no time for any planned incident response.


## [NAZ-L2] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`Kernel.sol#L77`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L77), [`Kernel.sol#L127`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L127), [`Kernel.sol#L251`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L251), [`Kernel.sol#L253`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L253), [`TRSRY.sol#L122`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L122), [`RANGE.sol#L242`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L242), [`RANGE.sol#L263`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L263), [`PRICE.sol#L240`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L240), [`PRICE.sol#L266`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L266), [`Operator.sol#L516`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L516), [`Operator.sol#L527`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L527), [`Operator.sol#L548`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L548), [`Operator.sol#L559`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L559), [`Operator.sol#L586`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L586), [`BondCallback.sol#L190`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L190), [`Heart.sol#L130`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L130), [`Heart.sol#L135`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L135), [`Heart.sol#L140`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L140)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L3] Missing Zero-address Validation
**Severity**: Low
**Context**: [`Kernel.sol#L77`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L77), [`Kernel.sol#L251`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L251), [`Kernel.sol#L253`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L253), [`BondCallback.sol#L190`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L190), [`Heart.sol#L140`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L140)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.


## [NAZ-L4] Lack of Event Emission For Critical Functions
**Severity**: Low
**Context**: [`Kernel.sol#L77`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L77), [`Kernel.sol#L127`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L127), [`Kernel.sol#L251`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L251), [`Kernel.sol#L253`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L253), [`BondCallback.sol#L190`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L190), [`Heart.sol#L130`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L130), [`Heart.sol#L135`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L135)

**Description**:
Several functions update critical parameters that are missing event emission. These should be performed to ensure tracking of changes of such critical parameters.

**Recommendation**:
Consider adding events to functions that change critical parameters.


## [NAZ-L5] Missing Events In Initialize Functions
**Severity**: Low
**Context**: [`PRICE.sol#L205`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205), [`Operator.sol#L598`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L598), [`PriceConfig.sol#L45`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45)

**Description**:
None of the initialize functions emit emit init-specific events. They all however have the initializer modifier (from Initializable) so that they can be called only once. Off-chain monitoring of calls to these critical functions is not possible.

**Recommendation**:
It is recommended to emit events in your initialization functions.


## [NAZ-N1] Unreachable Code
**Severity** Informational
**Context**: [`VOTES#L47`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L47)

**Description**:
There is unreachable code that can be removed to clean up the code.

**Recommendation**:
Consider removing the unreachable code to clean it up.



## [NAZ-N2] Votes Module `ERC20` Token Name `"OlympusDAO Dummy Voting Tokens"`
**Severity** Informational
**Context**: [`VOTES#L18`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L18)

**Description**:
This was probably meant as a joke during testing and should probably be renamed for production to not confuse users.

**Recommendation**:
Consider renaming the votes module `ERC20` token name `"OlympusDAO Dummy Voting Tokens"` to `"OlympusDAO Voting Tokens"`.


## [NAZ-N3] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`Kernel.sol#L131`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L131), [`PRICE.sol#L59`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59), [`TreasuryCustodian.sol#L20`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L20), [`Operator.sol#L69-L72`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L69-L72), [`Heart.sol#L45`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L45), [`PriceConfig.sol#L11`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L11)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Internal/private functions and variables should lead with an `_underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the Solidity style guide.


## [NAZ-N4] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`Kernel.sol#L71`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L71), [`Kernel.sol#L89`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L89), [`Kernel.sol#L120`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L120), [`Kernel.sol#L224-L230`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L224-L230), [`TRSRY.sol#L20-L39`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L20-L39), [`RANGE.sol#L20-L31`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L20-L31), [`PRICE.sol#L26-L28`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L26-L28), [`INSTR.sol#L11`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L11), [`TreasuryCustodian.sol#L17`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L17), [`Operator.sol#L45-L54`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L45-L54), [`Operator.sol#L188`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L188), [`Governance.sol#L61-L137`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L61-L137)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Some constructs deviate from this recommended best-practice: Modifiers are in the middle of contracts. External/public functions are mixed with internal/private ones. Few events are declared in contracts while most others are in corresponding interfaces.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N5] Comment Line Length
**Severity**: Informational
**Context**: [`RANGE.sol#L40`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L40), [`RANGE.sol#L44`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L44), [`RANGE.sol#L46-L48`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L46-L48), [`RANGE.sol#L61-L62`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L61-L62), [`RANGE.sol#L214`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L214), [`RANGE.sol#L239-L240`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L239-L240), [`RANGE.sol#L261-L262`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L261-L262), [`PRICE.sol#L19-L20`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L19-L20), [`PRICE.sol#L31`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L31), [`PRICE.sol#L39-L40`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L39-L40), [`PRICE.sol#L46`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L46), [`PRICE.sol#L78`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#l78), [`PRICE.sol#L120`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L120), [`PRICE.sol#L189`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L189), [`PRICE.sol#L201`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L201), [`PRICE.sol#L203`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L203), [`PRICE.sol#L263-L264`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L263-L264), [`Operator.sol#L97`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L97), [`Operator.sol#L199`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L199), [`Operator.sol#L481`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L481), [`Operator.sol#L657`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L657), [`Operator.sol#L730`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L730), [`Operator.sol#L734`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L734), [`PriceConfig.sol#L41`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L41), [`PriceConfig.sol#L43`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L43), [`PriceConfig.sol#L66-L67`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L66-L67), [`Governance.sol#L119`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L119), [`Governance.sol#L156`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L156), [`Governance.sol#L158`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L158), [`IBondCallback.sol#L7`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L7), [`IOperator.sol#L13`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L13), [`IOperator.sol#L15-L17`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L15-L17), [`IOperator.sol#L34`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L34), [`IOperator.sol#L72-L73`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L72-L73), [`IOperator.sol#L79`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L79), [`IOperator.sol#L84`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L84), [`IOperator.sol#L90-L91`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L90-L91), [`IOperator.sol#L100`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L100), [`IOperator.sol#L108`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L108), [`IOperator.sol#L124`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L124), [`IOperator.sol#L130`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L130), [`IOperator.sol#L135`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L135), [`IOperator.sol#L141`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L141)

**Description**:
Max line length must be no more than 120 but many comments are extended past this length.

**Recommendation**:
Consider cutting down the line length below 120.


## [NAZ-N6] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`Kernel.sol#L85`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L85), [`Kernel.sol#L95`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L95), [`Kernel.sol#L100`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L100), [`Kernel.sol#L105`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L105), [`Kernel.sol#L115`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L115), [`Kernel.sol#L139`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L139), [`Kernel.sol#L143`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L143), [`TRSRY.sol#L45`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L45), [`VOTES.sol#L19`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L19), [`INSTR.sol#L20`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L20), [`TreasuryCustodian.sol#L24`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L24), [`PriceConfig.sol#L15`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L15), [`Governance.sol#L59`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L59), [`VoterRegistration.sol#L16`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L16)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N7] Use Underscores for Number Literals
**Severity**: Informational
**Context**: [`RANGE.sol#L245`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L245), [`RANGE.sol#L247`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L247), [`RANGE.sol#L264`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L264), [`Operator.sol#L111`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L111), [`Operator.sol#L518`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L518), [`Operator.sol#L550`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L550), [`Governance.sol#L164`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L164)

**Description**:
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

**Recommendation**:
Consider using underscores for number literals to improve its readability.


## [NAZ-N8] TODOs Left In The Code
**Severity**: Informational
**Context**: [`TreasuryCustodian.sol#L51-L52`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L51-L52), [`TreasuryCustodian.sol#L56`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L56), [`Operator.sol#L657`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L657)

**Description**:
There should never be any TODOs in the code when deploying.

**Recommendation**:
Consider finishing the TODOs before deploying.


## [NAZ-N9] Spelling Errors
**Severity**: Informational
**Context**: [`PRICE.sol#L126 (numbe => number)`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L126), [`Operator.sol#L295 (deactive => deactivate)`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L295), [`Operator.sol#L326 (deactive => deactivate)`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L326)

**Description**:
Spelling errors in comments can cause confusion to both users and developers.

**Recommendation**:
Consider checking all misspellings to ensure they are corrected..


## [NAZ-N10] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-olympus/tree/main/src)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.


## [NAZ-N11] Older Version Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-olympus/tree/main/src)

**Description**:
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. 

**Recommendation**:
Consider using the most recent version.