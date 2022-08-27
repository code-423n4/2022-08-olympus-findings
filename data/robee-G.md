## Unnecessary equals boolean


Boolean variables can be checked within conditionals directly without the use of equality operators to true/false.

### Code instances:

        Governance.sol, 223: if (proposalHasBeenActivated[proposalId_] == true) {
        Governance.sol, 306: if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {
        Operator.sol, 355: if (id_ == RANGE.market(false)) {
        Operator.sol, 351: if (id_ == RANGE.market(true)) {



## Change transferFrom to transfer

'transferFrom(address(this), *, **)' could be replaced by the following more gas efficient 'transfer(*, **)'
               This replacement is more gas efficient and improves the code quality.

### Code instance:

        Governance.sol, 312 : VOTES.transferFrom(address(this), msg.sender, userVotes);



## Caching array length can save gas


Caching the array length is more gas efficient.
This is because access to a local variable in solidity is more efficient than query storage / calldata / memory.
We recommend to change from:    

    for (uint256 i=0; i<array.length; i++) { ... }

to: 

    uint len = array.length  
    for (uint256 i=0; i<len; i++) { ... }


### Code instance:

        Governance.sol, instructions, 278



## Unnecessary index init


In for loops you initialize the index to start from 0, but it already initialized to 0 in default and this assignment cost gas. 
It is more clear and gas efficient to declare without assigning 0 and will have the same meaning:

### Code instance:

        Kernel.sol, 397



## Storage double reading. Could save SLOAD

Reading a storage variable is gas costly (SLOAD). In cases of multiple read of a storage variable in the same scope, caching the first read (i.e saving as a local variable) can save gas and decrease the
 overall gas uses. The following is a list of functions and the storage variables that you read twice: 

### Code instance:

        PRICE.sol: nextObsIndex is read twice in getLastPrice



## Rearrange state variables

You can change the order of the storage variables to decrease memory uses.

### Code instance:

In Operator.sol,rearranging the storage fields can optimize to: 11 slots from: 13 slots.
The new order of types (you choose the actual variables):
        1. Status
        2. Config
        3. OlympusPrice
        4. OlympusRange
        5. OlympusTreasury
        6. OlympusMinter
        7. IBondAuctioneer
        8. IBondCallback
        9. ERC20
        10. ERC20
        11. uint32
        12. uint8
        13. uint8
        14. bool
        15. bool




## Use bytes32 instead of string to save gas whenever possible


    Use bytes32 instead of string to save gas whenever possible.
    String is a dynamic data structure and therefore is more gas consuming then bytes32.

    
### Code instance:

        OlympusERC20.sol (L39), string internal UNAUTHORIZED = "UNAUTHORIZED"; 



## Use != 0 instead of > 0


Using != 0 is slightly cheaper than > 0. (see https://github.com/code-423n4/2021-12-maple-findings/issues/75 for similar issue)


### Code instance:

        FullMath.sol, 35: change 'denominator > 0' to 'denominator != 0'



## Unnecessary cast


    
### Code instances:

        Role Kernel.sol.grantRole - unnecessary casting Role(role_)
        Kernel Kernel.sol._migrateKernel - unnecessary casting Kernel(newKernel_)



## Use unchecked to save gas for certain additive calculations that cannot overflow


You can use unchecked in the following calculations since there is no risk to overflow:

### Code instances:

        Operator.sol (L#209) - if ( uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&
        Operator.sol (L#215) - if ( uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&
        Operator.sol (L#395) - payoutToken: ohm, quoteToken: reserve, callbackAddr: address(callback), capacityInQuote: false, capacity: marketCapacity, formattedInitialPrice: initialPrice, formattedMinimumPrice: minimumPrice, debtBuffer: config_.cushionDebtBuffer, vesting: uint48(0), conclusion: uint48(block.timestamp + config_.cushionDuration), depositInterval: config_.cushionDepositInterval, scaleAdjustment: scaleAdjustment
        Governance.sol (L#212) - if (block.timestamp > proposal.submissionTimestamp + ACTIVATION_DEADLINE) {
        Heart.sol (L#94) - if (block.timestamp < lastBeat + frequency()) revert Heart_OutOfCycle(); 
        Operator.sol (L#447) - payoutToken: reserve, quoteToken: ohm, callbackAddr: address(callback), capacityInQuote: false, capacity: marketCapacity, formattedInitialPrice: initialPrice, formattedMinimumPrice: minimumPrice, debtBuffer: config_.cushionDebtBuffer, vesting: uint48(0), conclusion: uint48(block.timestamp + config_.cushionDuration), depositInterval: config_.cushionDepositInterval, scaleAdjustment: scaleAdjustment
        Governance.sol (L#272) - if (block.timestamp < activeProposal.activationTimestamp + EXECUTION_TIMELOCK) {
        Governance.sol (L#227) - if (block.timestamp < activeProposal.activationTimestamp + GRACE_PERIOD) {



## Inline one time use functions


The following functions are used exactly once. Therefore you can inline them and save gas and improve code clearness.
    

### Code instances:

        Kernel.sol, _upgradeModule
        Kernel.sol, _activatePolicy
        Kernel.sol, _reconfigurePolicies
        FullMath.sol, mulDiv
        Kernel.sol, _deactivatePolicy
        Operator.sol, _addObservation
        Kernel.sol, _pruneFromDependents
        Kernel.sol, _migrateKernel
        Kernel.sol, _installModule



## Cache powers of 10 used several times

You calculate the power of 10 every time you use it instead of caching it once as a constant variable and using it instead. 
Fix the following code lines: 

### Code instances:

Operator.sol, 374 : You should cache the used power of 10 as constant state variable since it's used several times (2):              uint256 oracleScale = 10**uint8(int8(PRICE.decimals()) - priceDecimals);

Operator.sol, 419 : You should cache the used power of 10 as constant state variable since it's used several times (2):              uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;

Operator.sol, 783 : You should cache the used power of 10 as constant state variable since it's used several times (3):                      10**ohmDecimals * 10**PRICE.decimals(),

Operator.sol, 784 : You should cache the used power of 10 as constant state variable since it's used several times (3):                      10**reserveDecimals * RANGE.price(true, true)

Operator.sol, 752 : You should cache the used power of 10 as constant state variable since it's used several times (3):                  10**reserveDecimals * RANGE.price(true, false),

Operator.sol, 764 : You should cache the used power of 10 as constant state variable since it's used several times (3):                  10**reserveDecimals * RANGE.price(true, true)

Operator.sol, 763 : You should cache the used power of 10 as constant state variable since it's used several times (3):                  10**ohmDecimals * 10**PRICE.decimals(),

Operator.sol, 753 : You should cache the used power of 10 as constant state variable since it's used several times (3):                  10**ohmDecimals * 10**PRICE.decimals()

Operator.sol, 429 : You should cache the used power of 10 as constant state variable since it's used several times (2):              uint256 oracleScale = 10**uint8(int8(oracleDecimals) - priceDecimals);

Operator.sol, 418 : You should cache the used power of 10 as constant state variable since it's used several times (2):              uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;




## Upgrade pragma to at least 0.8.4


Using newer compiler versions and the optimizer gives gas optimizations
and additional safety checks are available for free.

The advantages of versions 0.8.* over <0.8.0 are:

        1. Safemath by default from 0.8.0 (can be more gas efficient than library based safemath.)
        2. Low level inliner : from 0.8.2, leads to cheaper runtime gas. Especially relevant when the contract has small functions. For example, OpenZeppelin libraries typically have a lot of small helper functions and if they are not inlined, they cost an additional 20 to 40 gas because of 2 extra jump instructions and additional stack operations needed for function calls.
        3. Optimizer improvements in packed structs: Before 0.8.3, storing packed structs, in some cases used an additional storage read operation. After EIP-2929, if the slot was already cold, this means unnecessary stack operations and extra deploy time costs. However, if the slot was already warm, this means additional cost of 100 gas alongside the same unnecessary stack operations and extra deploy time costs.
        4. Custom errors from 0.8.4, leads to cheaper deploy time cost and run time cost. Note: the run time cost is only relevant when the revert condition is met. In short, replace revert strings by custom errors.
    
### Code instance:

        OlympusERC20.sol



## Do not cache msg.sender


We recommend not to cache msg.sender since calling it is 2 gas while reading a variable is more.


### Code instance:

        https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L219

