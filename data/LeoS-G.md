# Low effect on readability

## [G-01] Use `!= 0` instead of `> 0` for unsigned integers.
A `uint` can't be below zero, so `!= 0` is sufficient and is gas more efficient.

1 instance:
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L247

Consider replacing `>` by `!=`.

*save 3 gas*

## [G-02] Unnecessary initialization of variable 
Some data type have a default value which is already the desired one. The default value of `uint` is `0`, it is so unnecessary to initialize these again.

3 instances:
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L397
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L43
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L58

Consider removing `= 0`

*save 3 gas each*

## [G-03] Transformation of post-increment to pre-increment
A pre-increment is cheaper than a post one. When it is possible, it is a good practice to apply pre-increment.

5 instances:
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L488
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L670
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L686

Consider transforming those.

With those changes, these evolutions in gas average report can be observe:

    Operator: operate: 122263 -> 122255 (-8)

## [G-04] Expression like `x = x + y` are cheaper than `x += y` for states variables.

4 instances:
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L135-L139
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L222
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103

Consider replacing `+=` and `-=`


With those changes, these evolutions in gas average report can be observe:

    OlympusPrice: Deployment: 1117743 -> 1115143 (-2600)
    OlympusHeart: Deployment: 934119 -> 932719 (-1400)
    OlympusHeart: beat: 29228 -> 29221 (-7)

## [G-05] Some operations can be marked unchecked
If an operation can't overflow, it is cheaper to mark it as unchecked to avoid the automatic check of overflow.
In this case:

    while  (price_ >=  10)  {
		price_ = price_ /  10;
		decimals++;
	}
The operation can't overflow or undeflow

1 instances
 - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L486-L489


Consider marking it unchecked

With this changes, these evolutions in gas average report can be observe:

    Operator: Deployment: 4679925 -> 4670317 (-9608)
    Operator: operate: 122263 -> 121936 (-327)

This part can already be subjected to two improvements, however this one is still largely ineffective, especially for large numbers up to 2^256. It would be very useful to import a log10 function from an external mathematical library. The gain can be very important.

## [G-06] Unnecessary public constant
Declaring a private constant is cheaper than a public one. In some case, a constant can be declared as private to save gas. It is the case if the constant don't need to be called outside the contract. A user could still read the value directly in the code instead of calling it, if needed.

8 instances:
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L65
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L121-L137
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89

Consider changing those constants to private. (The code still pass all the test with these changes.)

With those changes, these evolutions in gas average report can be observe:

    OlympusRange: Deployment: 1125279 -> 1121272 (-4007)
    OlympusRange: spread: 655 -> 545 (-110)
    OlympusGovernance: Deployment: 1638243 -> 1604601 (-33642)
    OlympusGovernance: activateProposal: 52753 -> 52730 (-23)
    OlympusGovernance: configureDependencies: 48513 -> 48490 (-23)
    OlympusGovernance: endorseProposal: 39015 -> 39037 (+22)
    OlympusGovernance: executeProposal: 171376 -> 171467 (+91)
    OlympusGovernance: getMetadata: 2195 -> 2172 (-23)
    OlympusGovernance: isActive: 696 -> 740 (+44)
    OlympusGovernance: noVotesForProposal: 549 -> 571 (+22)
    OlympusGovernance: proposalHasBeenActivated:486 -> 463 (-23)
    OlympusGovernance: reclaimVotes: 10009 -> 9927 (82)
    OlympusGovernance: requestPermissions: 2953 -> 2997 (+44)
    OlympusGovernance: tokenClaimsForProposal: 684 -> 728 (+44)
    OlympusGovernance: totalEndorsementsForProposal: 529 -> 506 (-23)
    OlympusGovernance: userEndorsementsForProposal: 727 ->  639 (-88)
    OlympusGovernance: userVotesForProposal: 662 -> 706 (-23)
    OlympusGovernance: vote: 61568 -> 61612 (+44)
    OlympusGovernance: yesVotesForProposal: 506 -> 483 (-23)
    Operator: Deployment: 4679925 -> 4671717 (-8208)
    Operator: auctioneer: 437 -> 372 (-65)
    Operator: callback: 439 -> 372 (-67)
    Operator: config: 1224 -> 1246 (+19)
    Operator: configureDependencies: 121016 -> 121038 (+22)
    Operator: fullCapacity: 5237 -> 5204 (-33)
    Operator: initialize: 316017 -> 315844 (-173)
    Operator: initialized: 1356 -> 1379 (+23)
    Operator: isActive: 439 -> 373 (-66)
    Operator: operate: 122263 -> 122281 (+18)
    Operator: regenerate: 17622 -> 17612 (-10)
    Operator: requestPermissions: 6634 -> 6656 (+22)
    Operator: setBondContracts: 5267 -> 5289 (+22)
    Operator: setRegenParams: 11480 -> 11413 (-67)
    Operator: setSpreads: 9650 -> 9672 (+22)
    Operator: setThresholdFactor: 12113 -> 12135 (+22)
    Operator: status: 8988 -> 9010 (+22)
    Operator: swap: 54322 -> 54342 (+20)
    Operator: toggleActive: 7460 -> 7482 (+22)

## [G-07] Using `storage` instead of `memory`  can be cheaper.

A `storage` structure is pre allocated by the contract, by contrast, a `memory` one is newly created. Depending on the case both can be used to optimize the gas cost because simply, a `storage` is cheaper to create but more expensive to read from and to return and a `memory` on the other hand is more expensive to create but cheaper to read from and to return. We can optimize with trials and errors instead of complex calculations (which will probably work a bit better, but it's not done here).

Following this, we can deduce 7 cases that can be swapped to optimize runtime cost and deployment cost:
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L379
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L179
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L206
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L206
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L385
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L440
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L666

Consider changing `memory` to `storage` in these lines

With these changes, these evolutions in gas average report can be observed:

    Kernel: Deployment:  1473364->1456343 (-17021)
    OlympusRange: Deployment:   1125279 -> 1121272 (-4007)
    OlympusRange: spread: 655 -> 545 (-110)
    BondCallback: Deployment: 1408325 ->  1391912  (-16413)
    BondCallback: amountsForMarket: 1921 -> 1669 (-252)
    OlympusGovernance: Deployment: 1638243 -> 1596194 (-42049)
    OlympusGovernance: activateProposal: 52753 -> 51723 (-1030)
    Operator: Deployment: 4679925 -> 4566769 (-113156)
    Operator: fullCapacity: 5237 -> 5182 (-55)
    Operator: initialize: 316017 -> 315911 (-106)
    Operator: operate: 122263 -> 118511 (-3752)
    Operator: regenerate: 17622 -> 17593 (-29)
    ModuleTestFixture :Deployment: 422065 -> 399069 (-22996)



## [G-08] Using `calldata` instead of `memory` for read only argument in external function
If a function parameter is read only, it is cheaper in gas to use `calldata` instead of `memory`.

4 instances:
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L152
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L53

Consider changing `memory` to `calldata` in these lines/

With these changes, these evolutions in gas average report can be observed:

    OlympusPrice: Deployment: 1117743 -> 1101930 (-15813)
    OlympusPrice: initialize: 432495 -> 430562 (-1933)
    BondCallback: Deployment: 1408325 -> 1386305 (-22020)
    BondCallback: batchToTreasury: 12729 -> 12543 (-186)
    OlympusPriceConfig: initialize: 491657 -> 486274 (-5383)
    TreasuryCustodian: Deployment: 739696 -> 719277 (-20419)
    TreasuryCustodian: revokePolicyApprovals: 6956 -> 6842 (-114)

# Medium effect in use
## [G-09] `external` function for the admin can be marked as `payable`

If a function is guaranteed to revert when called by a normal user, this function can be marked as `payable` to avoid the check to know if a payment is provided.

2 instances:
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L451

Consider adding `payable` keyword.

*Save 21 gas cost*

# High effect on readability

## [G-10] Optimise function name
Every function have a keccak256 hash, this hash defines the order of the function in the contract. The best the ranking, the minimum the gas usage to access the function. Each time a function is called, the EVM need to pass through all the functions better ranked (going through a function cost 22 gas), and this operation cost gas. This can be optimized, the ranking is defined by the first four bytes of the kekkack256 hash of the function name. The name can be changed to improve the ranking, which greatly impacts the readability. That's why it's not practical to change all the names, but it's possible to change only the ones of the functions called a lot of times. This change can be done on the following functions according to their number of uses in the tests and their current ranking.

1. `Kernel.sol`: f166d9eb - `modulePermissions(bytes5,address,bytes4)`
**Must outrank:** 000dd95d - `moduleDependents(bytes5,uint256)`
**New name:** 00097fbb - `modulePermissions_1055(bytes5,address,bytes4)`
**Rank:** 14 -> 1
*Save 286 gas each call*

2. `Kernel.sol`: c4d1f8f1 -  `executeAction(uint8,address)`
**Must outrank:** 000dd95d - `moduleDependents(bytes5,uint256)`
**New name:** 000a8da2 - `executeAction_11563(uint8,address)`
**Rank:** 11 -> 2
*Save 198 gas each call*

3. `MINTR.sol`: 1ae7ec2e -  `KEYCODE()`
**Must outrank:** 02b1d239 - `ohm()`
**New name:** 00906b26 - `KEYCODE_342()`
**Rank:** 2-> 1
*Save 22 gas each call*

4. `RANGE.sol`: bf30142b - `capacity(bool)`
**Must outrank:** 00d16739 - `regenerate(bool,uint256)`
**New name:** 00e60c55 - `capacity_81(bool)`
**Rank:** 14 -> 1
*Save 286 gas each call*

5. `TRSRY.sol`: 1ae7ec2e -  `KEYCODE()`
**Must outrank:** 15226b54 - `getReserveBalance(address)`
**New name:** 00906b26 - `KEYCODE_342()`
**Rank:** 2-> 1
*Save 22 gas each call*

6. `VOTE.sol`: 1ae7ec2e - `KEYCODE()`
**Must outrank:** 06fdde03 - `name()`
**New name:** 00906b26 - `KEYCODE_342()`
**Rank:** 3 -> 1
*Save 44 gas each call*

7. `Governance.sol`: d1755067 - `endorseProposal(uint256)`
**Must outrank:** 01153876 - `proposalHasBeenActivated(uint256)`
**New name:** 007fedae - `endorseProposal_861(uint256)`
**Rank:** 22 -> 1
*Save 462 gas each call*

8. `Governance.sol`: 9459b875 - `configureDependencies()`
**Must outrank:** 01153876 - `proposalHasBeenActivated(uint256)`
**New name:** 00aced39 - `configureDependencies_1382()`
**Rank:** 18 -> 2
*Save gas each call*

9. `Operator.sol`: 7159a618 - `operate()`
**Must outrank:** 01de9ba8 - `setReserveFactor(uint32)`
**New name:** 000b8875 - `operate_53()`
**Rank:** 18 -> 1
*Save 352 gas each call*

10. `Operator.sol`: ec7404b1 - `setActiveStatus(bool)`
**Must outrank:** 01de9ba8 - `setReserveFactor(uint32)`
**New name:** 00d3138f - `setActiveStatus_78(bool)`
**Rank:** 31 -> 2
*Save 638 gas each call*

Consider optimizing these function names.