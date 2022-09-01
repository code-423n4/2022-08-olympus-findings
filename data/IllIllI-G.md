## Summary

### Gas Optimizations
| |Issue|Instances|
|-|:-|:-:|
| [G&#x2011;01] | State variables should only be updated once in a function | 1 |
| [G&#x2011;02] | Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate | 1 |
| [G&#x2011;03] | State variables only set in the constructor should be declared `immutable` | 11 |
| [G&#x2011;04] | State variables can be packed into fewer storage slots | 1 |
| [G&#x2011;05] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 5 |
| [G&#x2011;06] | Using `storage` instead of `memory` for structs/arrays saves gas | 4 |
| [G&#x2011;07] | State variables should be cached in stack variables rather than re-reading them from storage | 7 |
| [G&#x2011;08] | Multiple accesses of a mapping/array should use a local variable cache | 1 |
| [G&#x2011;09] | The result of function calls should be cached rather than re-calling the function | 3 |
| [G&#x2011;10] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 3 |
| [G&#x2011;11] | `internal` functions only called once can be inlined to save gas | 9 |
| [G&#x2011;12] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 1 |
| [G&#x2011;13] | `<array>.length` should not be looked up in every loop of a `for`-loop | 1 |
| [G&#x2011;14] | Optimize names to save gas | 20 |
| [G&#x2011;15] | Using `bool`s for storage incurs overhead | 11 |
| [G&#x2011;16] | Use a more recent version of solidity | 3 |
| [G&#x2011;17] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 7 |
| [G&#x2011;18] | Using `private` rather than `public` for constants, saves gas | 11 |
| [G&#x2011;19] | Don't compare boolean expressions to boolean literals | 2 |
| [G&#x2011;20] | Division by two should use bit shifting | 2 |
| [G&#x2011;21] | Superfluous event fields | 7 |
| [G&#x2011;22] | Functions guaranteed to revert when called by normal users can be marked `payable` | 36 |

Total: 147 instances over 22 issues

The source diffs can be directly applied to the code by putting the diff block into a file then doing `git apply <file>`

## Gas Optimizations

### [G&#x2011;01]  State variables should only be updated once in a function
`totalEndorsementsForProposal` is updated twice in this function, but it could be optimized to only update the difference between `previousEndorsement` and `userVotes` instead. Futher optimizations would be to use a `storage` variable rather than looking up the hash of `totalEndorsementsForProposal[proposalId_]` each time, and to use `x = x + a` rather than `x += a`

*There is 1 instance of this issue:*
```solidity
File: /src/policies/Governance.sol

192          // undo any previous endorsement the user made on these instructions
193          uint256 previousEndorsement = userEndorsementsForProposal[proposalId_][msg.sender];
194          totalEndorsementsForProposal[proposalId_] -= previousEndorsement;
195  
196          // reapply user endorsements with most up-to-date votes
197          userEndorsementsForProposal[proposalId_][msg.sender] = userVotes;
198:         totalEndorsementsForProposal[proposalId_] += userVotes;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L192-L198

```diff
diff --git a/src/policies/Governance.sol b/src/policies/Governance.sol
index 8829e3b..c0e783f 100644
--- a/src/policies/Governance.sol
+++ b/src/policies/Governance.sol
@@ -191,11 +191,14 @@ contract OlympusGovernance is Policy {
 
         // undo any previous endorsement the user made on these instructions
         uint256 previousEndorsement = userEndorsementsForProposal[proposalId_][msg.sender];
-        totalEndorsementsForProposal[proposalId_] -= previousEndorsement;
 
         // reapply user endorsements with most up-to-date votes
         userEndorsementsForProposal[proposalId_][msg.sender] = userVotes;
-        totalEndorsementsForProposal[proposalId_] += userVotes;
+        if (previousEndorsement > userVotes) {
+            totalEndorsementsForProposal[proposalId_] -= (previousEndorsement - userVotes);
+        } else {
+            totalEndorsementsForProposal[proposalId_] += (userVotes - previousEndorsement);
+        }
 
         emit ProposalEndorsed(proposalId_, msg.sender, userVotes);
     }
```

Note that the numbers below are an underreporting of the gas changes due to [this](https://gist.github.com/0xA5DF/cc71e507d45fd51d708b3e8318654ce5) `forge` issue
```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index c793374..828386f 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -289,7 +289,7 @@
 ╞════════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
 │ Deployment Cost                                        ┆ Deployment Size ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 1638243                                                ┆ 8250            ┆        ┆        ┆        ┆         │
+│ 1642043                                                ┆ 8269            ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                          ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -297,7 +297,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ configureDependencies                                  ┆ 47868           ┆ 48513  ┆ 47868  ┆ 51868  ┆ 31      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ endorseProposal                                        ┆ 6874            ┆ 39015  ┆ 30774  ┆ 52674  ┆ 43      │
+│ endorseProposal                                        ┆ 6476            ┆ 38636  ┆ 30376  ┆ 52276  ┆ 43      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ executeProposal                                        ┆ 1850            ┆ 171376 ┆ 238748 ┆ 238748 ┆ 7       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
```

### [G&#x2011;02]  Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There is 1 instance of this issue:*
```solidity
File: src/policies/Governance.sol

96        mapping(uint256 => ProposalMetadata) public getProposalMetadata;
97    
98        /// @notice Return the total endorsements for a proposal id.
99        mapping(uint256 => uint256) public totalEndorsementsForProposal;
100   
101       /// @notice Return the number of endorsements a user has given a proposal id.
102       mapping(uint256 => mapping(address => uint256)) public userEndorsementsForProposal;
103   
104       /// @notice Return whether a proposal id has been activated. Once this is true, it should never be flipped false.
105       mapping(uint256 => bool) public proposalHasBeenActivated;
106   
107       /// @notice Return the total yes votes for a proposal id used in calculating net votes.
108       mapping(uint256 => uint256) public yesVotesForProposal;
109   
110       /// @notice Return the total no votes for a proposal id used in calculating net votes.
111       mapping(uint256 => uint256) public noVotesForProposal;
112   
113       /// @notice Return the amount of votes a user has applied to a proposal id. This does not record how the user voted.
114       mapping(uint256 => mapping(address => uint256)) public userVotesForProposal;
115   
116       /// @notice Return the amount of tokens reclaimed by a user after voting on a proposal id.
117:      mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L96-L117

### [G&#x2011;03]  State variables only set in the constructor should be declared `immutable`
Avoids a Gsset (**20000 gas**) in the constructor, and replaces the first access in each transaction (Gcoldsload - **2100 gas**) and each access thereafter (Gwarmacces - **100 gas**) with a `PUSH32` (**3 gas**).

*There are 11 instances of this issue:*
```solidity
File: src/policies/BondCallback.sol

/// @audit aggregator (constructor)
43:           aggregator = aggregator_;

/// @audit aggregator (access)
91:           (, , ERC20 payoutToken, , , ) = aggregator.getAuctioneer(id_).getMarketInfoForPurchase(id_);

/// @audit aggregator (access)
109:          (, , ERC20 payoutToken, ERC20 quoteToken, , ) = aggregator

/// @audit ohm (constructor)
44:           ohm = ohm_;

/// @audit ohm (access)
57:           ohm.safeApprove(address(MINTR), type(uint256).max);

/// @audit ohm (access)
94:           if (address(payoutToken) != address(ohm)) {

/// @audit ohm (access)
118:          if (quoteToken == payoutToken && quoteToken == ohm) {

/// @audit ohm (access)
125:          } else if (quoteToken == ohm) {

/// @audit ohm (access)
131:          } else if (payoutToken == ohm) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L43

```solidity
File: src/policies/Heart.sol

/// @audit _operator (constructor)
60:           _operator = operator_;

/// @audit _operator (access)
100:          _operator.operate();

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L60

```diff
diff --git a/src/policies/BondCallback.sol b/src/policies/BondCallback.sol
index 4da1a3a..4383f7e 100644
--- a/src/policies/BondCallback.sol
+++ b/src/policies/BondCallback.sol
@@ -25,11 +25,11 @@ contract BondCallback is Policy, ReentrancyGuard, IBondCallback {
     mapping(uint256 => uint256[2]) internal _amountsPerMarket;
     mapping(ERC20 => uint256) public priorBalances;
 
-    IBondAggregator public aggregator;
+    IBondAggregator immutable public aggregator;
     OlympusTreasury public TRSRY;
     OlympusMinter public MINTR;
     Operator public operator;
-    ERC20 public ohm;
+    ERC20 immutable public ohm;
 
     /*//////////////////////////////////////////////////////////////
                             POLICY INTERFACE
diff --git a/src/policies/Heart.sol b/src/policies/Heart.sol
index 7693dba..b0a46f2 100644
--- a/src/policies/Heart.sol
+++ b/src/policies/Heart.sol
@@ -45,7 +45,7 @@ contract OlympusHeart is IHeart, Policy, ReentrancyGuard {
     OlympusPrice internal PRICE;
 
     // Policies
-    IOperator internal _operator;
+    IOperator immutable internal _operator;
 
     /*//////////////////////////////////////////////////////////////
                             POLICY INTERFACE
```

Note that the numbers below are an underreporting of the gas changes due to [this](https://gist.github.com/0xA5DF/cc71e507d45fd51d708b3e8318654ce5) `forge` issue
```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index c793374..1cea2f8 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -24,7 +24,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ allKeycodes                    ┆ 706             ┆ 706    ┆ 706    ┆ 706    ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ executeAction                  ┆ 649             ┆ 156798 ┆ 94565  ┆ 595110 ┆ 767     │
+│ executeAction                  ┆ 649             ┆ 156789 ┆ 94565  ┆ 595110 ┆ 767     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ executor                       ┆ 2393            ┆ 2393   ┆ 2393   ┆ 2393   ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -258,7 +258,7 @@
 ╞═════════════════════════════════════════════════════╪═════════════════╪═══════╪════════╪════════╪═════════╡
 │ Deployment Cost                                     ┆ Deployment Size ┆       ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 1408325                                             ┆ 6934            ┆       ┆        ┆        ┆         │
+│ 1417471                                             ┆ 7248            ┆       ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                       ┆ min             ┆ avg   ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -268,9 +268,9 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ batchToTreasury                                     ┆ 4111            ┆ 12729 ┆ 12068  ┆ 22668  ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ callback                                            ┆ 7678            ┆ 98980 ┆ 85627  ┆ 187927 ┆ 17      │
+│ callback                                            ┆ 7678            ┆ 97563 ┆ 85333  ┆ 183633 ┆ 17      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ configureDependencies                               ┆ 73564           ┆ 73564 ┆ 73564  ┆ 73564  ┆ 63      │
+│ configureDependencies                               ┆ 73464           ┆ 73464 ┆ 73464  ┆ 73464  ┆ 63      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ isActive                                            ┆ 395             ┆ 395   ┆ 395    ┆ 395    ┆ 63      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -282,7 +282,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ setOperator                                         ┆ 8227            ┆ 23461 ┆ 23865  ┆ 23865  ┆ 65      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ whitelist                                           ┆ 8221            ┆ 39608 ┆ 34675  ┆ 67005  ┆ 46      │
+│ whitelist                                           ┆ 8221            ┆ 38018 ┆ 32143  ┆ 62802  ┆ 46      │
 ╰─────────────────────────────────────────────────────┴─────────────────┴───────┴────────┴────────┴─────────╯
 ╭────────────────────────────────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
 │ src/policies/Governance.sol:OlympusGovernance contract ┆                 ┆        ┆        ┆        ┆         │
@@ -336,13 +336,13 @@
 ╞══════════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
 │ Deployment Cost                              ┆ Deployment Size ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 934119                                       ┆ 4277            ┆       ┆        ┆       ┆         │
+│ 914213                                       ┆ 4290            ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ active                                       ┆ 323             ┆ 989   ┆ 323    ┆ 2323  ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ beat                                         ┆ 5429            ┆ 29228 ┆ 18552  ┆ 61386 ┆ 8       │
+│ beat                                         ┆ 5429            ┆ 28154 ┆ 17478  ┆ 59238 ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ configureDependencies                        ┆ 24123           ┆ 24123 ┆ 24123  ┆ 24123 ┆ 11      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -397,7 +397,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ isActive                                    ┆ 439             ┆ 439    ┆ 439    ┆ 439    ┆ 63      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ operate                                     ┆ 387             ┆ 122263 ┆ 37958  ┆ 640609 ┆ 104     │
+│ operate                                     ┆ 387             ┆ 121697 ┆ 37958  ┆ 636406 ┆ 104     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ regenerate                                  ┆ 3772            ┆ 17622  ┆ 21791  ┆ 29292  ┆ 6       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -577,7 +577,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ getFee                                                                  ┆ 872             ┆ 3538   ┆ 4872   ┆ 4872   ┆ 6       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ purchase                                                                ┆ 228899          ┆ 239488 ┆ 239488 ┆ 250077 ┆ 2       │
+│ purchase                                                                ┆ 228739          ┆ 239261 ┆ 239261 ┆ 249783 ┆ 2       │
 ╰─────────────────────────────────────────────────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯
 ╭────────────────────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
 │ src/test/mocks/KernelTestMocks.sol:MockModule contract ┆                 ┆       ┆        ┆       ┆         │
```


### [G&#x2011;04]  State variables can be packed into fewer storage slots
If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper

*There is 1 instance of this issue:*
```solidity
File: src/policies/Heart.sol

/// @audit Variable ordering with 5 slots instead of the current 6:
///           uint256(32):lastBeat, uint256(32):reward, user-defined(20):rewardToken, bool(1):active, address(20):PRICE, address(20):_operator
33:       bool public active;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L33

```diff
diff --git a/src/policies/Heart.sol b/src/policies/Heart.sol
index 7693dba..ced0bcb 100644
--- a/src/policies/Heart.sol
+++ b/src/policies/Heart.sol
@@ -29,9 +29,6 @@ contract OlympusHeart is IHeart, Policy, ReentrancyGuard {
     event RewardIssued(address to_, uint256 rewardAmount_);
     event RewardUpdated(ERC20 token_, uint256 rewardAmount_);
 
-    /// @notice Status of the Heart, false = stopped, true = beating
-    bool public active;
-
     /// @notice Timestamp of the last beat (UTC, in seconds)
     uint256 public lastBeat;
 
@@ -41,6 +38,9 @@ contract OlympusHeart is IHeart, Policy, ReentrancyGuard {
     /// @notice Reward token address that users are sent for beating the Heart
     ERC20 public rewardToken;
 
+    /// @notice Status of the Heart, false = stopped, true = beating
+    bool public active;
+
     // Modules
     OlympusPrice internal PRICE;

```

Note that the numbers below are an underreporting of the gas changes due to [this](https://gist.github.com/0xA5DF/cc71e507d45fd51d708b3e8318654ce5) `forge` issue
```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index c793374..9682b00 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -336,13 +336,13 @@
 ╞══════════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
 │ Deployment Cost                              ┆ Deployment Size ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 934119                                       ┆ 4277            ┆       ┆        ┆       ┆         │
+│ 917440                                       ┆ 4309            ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ active                                       ┆ 323             ┆ 989   ┆ 323    ┆ 2323  ┆ 3       │
+│ active                                       ┆ 340             ┆ 1006  ┆ 340    ┆ 2340  ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ beat                                         ┆ 5429            ┆ 29228 ┆ 18552  ┆ 61386 ┆ 8       │
+│ beat                                         ┆ 5443            ┆ 28492 ┆ 18566  ┆ 59400 ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ configureDependencies                        ┆ 24123           ┆ 24123 ┆ 24123  ┆ 24123 ┆ 11      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -362,7 +362,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ setRewardTokenAndAmount                      ┆ 8222            ┆ 13892 ┆ 13892  ┆ 19562 ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ toggleBeat                                   ┆ 1400            ┆ 7187  ┆ 8455   ┆ 10440 ┆ 4       │
+│ toggleBeat                                   ┆ 1427            ┆ 8416  ┆ 9577   ┆ 13083 ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ withdrawUnspentRewards                       ┆ 8201            ┆ 19621 ┆ 19621  ┆ 31041 ┆ 2       │
 ╰──────────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
```


### [G&#x2011;05]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 5 instances of this issue:*
```solidity
File: src/modules/PRICE.sol

/// @audit startObservations_
205       function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
206           external
207:          permissioned

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L205-L207

```solidity
File: src/policies/BondCallback.sol

/// @audit tokens_
152:      function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L152

```solidity
File: src/policies/Governance.sol

/// @audit proposalURI_
159       function submitProposal(
160           Instruction[] calldata instructions_,
161           bytes32 title_,
162:          string memory proposalURI_

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L159-L162

```solidity
File: src/policies/PriceConfig.sol

/// @audit startObservations_
45        function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
46            external
47:           onlyRole("price_admin")

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L45-L47

```solidity
File: src/policies/TreasuryCustodian.sol

/// @audit tokens_
53:       function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L53

```diff
diff --git a/src/modules/PRICE.sol b/src/modules/PRICE.sol
index 55d85d3..5a620d7 100644
--- a/src/modules/PRICE.sol
+++ b/src/modules/PRICE.sol
@@ -202,7 +202,7 @@ contract OlympusPrice is Module {
     /// @param  lastObservationTime_ - Unix timestamp of last observation being provided (in seconds).
     /// @dev    This function must be called after the Price module is deployed to activate it and after updating the observationFrequency
     ///         or movingAverageDuration (in certain cases) in order for the Price module to function properly.
-    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
+    function initialize(uint256[] calldata startObservations_, uint48 lastObservationTime_)
         external
         permissioned
     {
diff --git a/src/policies/BondCallback.sol b/src/policies/BondCallback.sol
index 4da1a3a..902bcfc 100644
--- a/src/policies/BondCallback.sol
+++ b/src/policies/BondCallback.sol
@@ -149,7 +149,7 @@ contract BondCallback is Policy, ReentrancyGuard, IBondCallback {
 
     /// @notice Send tokens to the TRSRY in a batch
     /// @param  tokens_ - Array of tokens to send
-    function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {
+    function batchToTreasury(ERC20[] calldata tokens_) external onlyRole("callback_admin") {
         ERC20 token;
         uint256 balance;
         uint256 len = tokens_.length;
diff --git a/src/policies/Governance.sol b/src/policies/Governance.sol
index 8829e3b..a39dad0 100644
--- a/src/policies/Governance.sol
+++ b/src/policies/Governance.sol
@@ -159,7 +159,7 @@ contract OlympusGovernance is Policy {
     function submitProposal(
         Instruction[] calldata instructions_,
         bytes32 title_,
-        string memory proposalURI_
+        string calldata proposalURI_
     ) external {
         if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
             revert NotEnoughVotesToPropose();
diff --git a/src/policies/PriceConfig.sol b/src/policies/PriceConfig.sol
index 78887fd..214e0ab 100644
--- a/src/policies/PriceConfig.sol
+++ b/src/policies/PriceConfig.sol
@@ -42,7 +42,7 @@ contract OlympusPriceConfig is Policy {
     /// @param lastObservationTime_ Unix timestamp of last observation being provided (in seconds).
     /// @dev This function must be called after the Price module is deployed to activate it and after updating the observationFrequency
     ///      or movingAverageDuration (in certain cases) in order for the Price module to function properly.
-    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
+    function initialize(uint256[] calldata startObservations_, uint48 lastObservationTime_)
         external
         onlyRole("price_admin")
     {
diff --git a/src/policies/TreasuryCustodian.sol b/src/policies/TreasuryCustodian.sol
index 1c12f2e..a240cd1 100644
--- a/src/policies/TreasuryCustodian.sol
+++ b/src/policies/TreasuryCustodian.sol
@@ -50,7 +50,7 @@ contract TreasuryCustodian is Policy {
     // Anyone can call to revoke a deactivated policy's approvals.
     // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
     // TODO must reorg policy storage to be able to check for deactivated policies.
-    function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {
+    function revokePolicyApprovals(address policy_, ERC20[] calldata tokens_) external {
         if (Policy(policy_).isActive()) revert PolicyStillActive();
 
         // TODO Make sure `policy_` is an actual policy and not a random address.

```

Note that the numbers below are an underreporting of the gas changes due to [this](https://gist.github.com/0xA5DF/cc71e507d45fd51d708b3e8318654ce5) `forge` issue
```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index c793374..513078c 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -108,7 +108,7 @@
 ╞═════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
 │ Deployment Cost                             ┆ Deployment Size ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 1117743                                     ┆ 6851            ┆        ┆        ┆        ┆         │
+│ 1101930                                     ┆ 6772            ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                               ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -128,7 +128,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ getMovingAverage                            ┆ 544             ┆ 812    ┆ 544    ┆ 2420   ┆ 7       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ initialize                                  ┆ 4063            ┆ 432495 ┆ 512316 ┆ 886622 ┆ 24      │
+│ initialize                                  ┆ 2327            ┆ 430562 ┆ 510516 ┆ 883159 ┆ 24      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ initialized                                 ┆ 340             ┆ 955    ┆ 340    ┆ 2340   ┆ 13      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -258,7 +258,7 @@
 ╞═════════════════════════════════════════════════════╪═════════════════╪═══════╪════════╪════════╪═════════╡
 │ Deployment Cost                                     ┆ Deployment Size ┆       ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 1408325                                             ┆ 6934            ┆       ┆        ┆        ┆         │
+│ 1386305                                             ┆ 6824            ┆       ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                       ┆ min             ┆ avg   ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -266,7 +266,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ approvedMarkets                                     ┆ 685             ┆ 685   ┆ 685    ┆ 685    ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ batchToTreasury                                     ┆ 4111            ┆ 12729 ┆ 12068  ┆ 22668  ┆ 4       │
+│ batchToTreasury                                     ┆ 3800            ┆ 12543 ┆ 11920  ┆ 22533  ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ callback                                            ┆ 7678            ┆ 98980 ┆ 85627  ┆ 187927 ┆ 17      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -289,7 +289,7 @@
 ╞════════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
 │ Deployment Cost                                        ┆ Deployment Size ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 1638243                                                ┆ 8250            ┆        ┆        ┆        ┆         │
+│ 1645850                                                ┆ 8288            ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                          ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -317,7 +317,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ setActiveStatus                                        ┆ 777             ┆ 1228   ┆ 777    ┆ 3577   ┆ 31      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ submitProposal                                         ┆ 11529           ┆ 176506 ┆ 187247 ┆ 187247 ┆ 25      │
+│ submitProposal                                         ┆ 11364           ┆ 176510 ┆ 187258 ┆ 187258 ┆ 25      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ tokenClaimsForProposal                                 ┆ 684             ┆ 684    ┆ 684    ┆ 684    ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -430,7 +430,7 @@
 ╞══════════════════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
 │ Deployment Cost                                          ┆ Deployment Size ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 639600                                                   ┆ 3262            ┆        ┆        ┆        ┆         │
+│ 620181                                                   ┆ 3165            ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                            ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -440,7 +440,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ configureDependencies                                    ┆ 24144           ┆ 24144  ┆ 24144  ┆ 24144  ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ initialize                                               ┆ 10107           ┆ 491657 ┆ 524236 ┆ 895872 ┆ 7       │
+│ initialize                                               ┆ 8371            ┆ 486274 ┆ 519133 ┆ 885906 ┆ 7       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ isActive                                                 ┆ 317             ┆ 317    ┆ 317    ┆ 317    ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -453,7 +453,7 @@
 ╞═══════════════════════════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
 │ Deployment Cost                                               ┆ Deployment Size ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 739696                                                        ┆ 3762            ┆       ┆        ┆       ┆         │
+│ 719277                                                        ┆ 3660            ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                                 ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -469,7 +469,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ requestPermissions                                            ┆ 2061            ┆ 2477  ┆ 2061   ┆ 4561  ┆ 6       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ revokePolicyApprovals                                         ┆ 6956            ┆ 6956  ┆ 6956   ┆ 6956  ┆ 1       │
+│ revokePolicyApprovals                                         ┆ 6842            ┆ 6842  ┆ 6842   ┆ 6842  ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ setActiveStatus                                               ┆ 733             ┆ 733   ┆ 733    ┆ 733   ┆ 6       │
 ╰───────────────────────────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
```




### [G&#x2011;06]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There are 4 instances of this issue:*
```solidity
File: src/policies/Operator.sol

206:          Config memory config_ = _config;

385:              Config memory config_ = _config;

440:              Config memory config_ = _config;

666:          Regen memory regen = _status.low;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L206

```diff
diff --git a/src/policies/Operator.sol b/src/policies/Operator.sol
index 7573526..e3c5c53 100644
--- a/src/policies/Operator.sol
+++ b/src/policies/Operator.sol
@@ -203,7 +203,7 @@ contract Operator is IOperator, Policy, ReentrancyGuard {
         _updateCapacity(false, 0);
 
         /// Cache config in memory
-        Config memory config_ = _config;
+        Config storage config_ = _config;
 
         /// Check if walls can regenerate capacity
         if (
@@ -437,7 +437,7 @@ contract Operator is IOperator, Policy, ReentrancyGuard {
             uint256 minimumPrice = invCushionPrice.mulDiv(bondScale, oracleScale);
 
             /// Cache config struct to avoid multiple SLOADs
-            Config memory config_ = _config;
+            Config storage config_ = _config;
 
             /// Calculate market capacity from the cushion factor
             uint256 marketCapacity = range.low.capacity.mulDiv(config_.cushionFactor, FACTOR_SCALE);
@@ -663,7 +663,7 @@ contract Operator is IOperator, Policy, ReentrancyGuard {
         /// Update low side regen status with a new observation
         /// Observation is positive if the current price is greater than the MA
         uint32 observe = _config.regenObserve;
-        Regen memory regen = _status.low;
+        Regen storage regen = _status.low;
         if (currentPrice >= movingAverage) {
             if (!regen.observations[regen.nextObservation]) {
                 _status.low.observations[regen.nextObservation] = true;
```

```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index c793374..0d24e75 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -371,7 +371,7 @@
 ╞═════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
 │ Deployment Cost                             ┆ Deployment Size ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 4679925                                     ┆ 25703           ┆        ┆        ┆        ┆         │
+│ 4594613                                     ┆ 25277           ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                               ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -397,7 +397,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ isActive                                    ┆ 439             ┆ 439    ┆ 439    ┆ 439    ┆ 63      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ operate                                     ┆ 387             ┆ 122263 ┆ 37958  ┆ 640609 ┆ 104     │
+│ operate                                     ┆ 387             ┆ 118525 ┆ 34414  ┆ 636359 ┆ 104     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ regenerate                                  ┆ 3772            ┆ 17622  ┆ 21791  ┆ 29292  ┆ 6       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
```

### [G&#x2011;07]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 7 instances of this issue:*
```solidity
File: src/modules/PRICE.sol

/// @audit _movingAverage on line 138
146:          emit NewObservation(block.timestamp, currentPrice, _movingAverage);

/// @audit nextObsIndex on line 185
185:          uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;

/// @audit numObservations on line 97
100:          observations = new uint256[](numObservations);

/// @audit observationFrequency on line 165
171:              if (updatedAt < block.timestamp - uint256(observationFrequency))

/// @audit observationFrequency on line 242
246:          uint256 newObservations = uint256(movingAverageDuration_ / observationFrequency);

/// @audit movingAverageDuration on line 268
272:          uint256 newObservations = uint256(movingAverageDuration / observationFrequency_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L146

```solidity
File: src/policies/Heart.sol

/// @audit reward on line 112
113:          emit RewardIssued(to_, reward);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L113

### [G&#x2011;08]  Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

*There is 1 instance of this issue:*
```solidity
File: src/Kernel.sol

/// @audit moduleDependents[keycode] on line 309
310:              getDependentIndex[keycode][policy_] = moduleDependents[keycode].length - 1;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L310

### [G&#x2011;09]  The result of function calls should be cached rather than re-calling the function
The instances below point to the second+ call of the function within a single function

*There are 3 instances of this issue:*
```solidity
File: src/policies/PriceConfig.sol

/// @audit PRICE.KEYCODE() on line 32
33:           permissions[1] = Permissions(PRICE.KEYCODE(), PRICE.changeMovingAverageDuration.selector);

/// @audit PRICE.KEYCODE() on line 32
34:           permissions[2] = Permissions(PRICE.KEYCODE(), PRICE.changeObservationFrequency.selector);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L33

```solidity
File: src/policies/VoterRegistration.sol

/// @audit VOTES.KEYCODE() on line 34
35:           permissions[1] = Permissions(VOTES.KEYCODE(), VOTES.burnFrom.selector);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/VoterRegistration.sol#L35

### [G&#x2011;10]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There are 3 instances of this issue:*
```solidity
File: src/modules/PRICE.sol

136:              _movingAverage += (currentPrice - earliestPrice) / numObs;

138:              _movingAverage -= (earliestPrice - currentPrice) / numObs;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L136

```solidity
File: src/policies/Heart.sol

103:          lastBeat += frequency();

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L103

### [G&#x2011;11]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 9 instances of this issue:*
```solidity
File: src/Kernel.sol

266:      function _installModule(Module newModule_) internal {

279:      function _upgradeModule(Module newModule_) internal {

295:      function _activatePolicy(Policy policy_) internal {

325:      function _deactivatePolicy(Policy policy_) internal {

351:      function _migrateKernel(Kernel newKernel_) internal {

378:      function _reconfigurePolicies(Keycode keycode_) internal {

409:      function _pruneFromDependents(Policy policy_) internal {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L266

```solidity
File: src/policies/Heart.sol

111:      function _issueReward(address to_) internal {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L111

```solidity
File: src/policies/Operator.sol

652       function _addObservation() internal {
653           /// Get latest moving average from the price module
654:          uint256 movingAverage = PRICE.getMovingAverage();

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L652-L654

### [G&#x2011;12]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There is 1 instance of this issue:*
```solidity
File: src/modules/PRICE.sol

/// @audit if-condition on line 135
136:              _movingAverage += (currentPrice - earliestPrice) / numObs;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L136

### [G&#x2011;13]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There is 1 instance of this issue:*
```solidity
File: src/policies/Governance.sol

278:          for (uint256 step; step < instructions.length; ) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L278

### [G&#x2011;14]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 20 instances of this issue:*
```solidity
File: src/interfaces/IBondCallback.sol

/// @audit callback(), amountsForMarket(), whitelist()
6:    interface IBondCallback {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/interfaces/IBondCallback.sol#L6

```solidity
File: src/Kernel.sol

/// @audit changeKernel()
62:   abstract contract KernelAdapter {

/// @audit KEYCODE(), VERSION(), INIT()
84:   abstract contract Module is KernelAdapter {

/// @audit setActiveStatus(), configureDependencies(), requestPermissions()
111:  abstract contract Policy is KernelAdapter {

/// @audit executeAction(), grantRole(), revokeRole()
149:  contract Kernel {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L62

```solidity
File: src/modules/INSTR.sol

/// @audit getInstructions(), store()
10:   contract OlympusInstructions is Module {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L10

```solidity
File: src/modules/MINTR.sol

/// @audit mintOhm(), burnOhm()
8:    contract OlympusMinter is Module {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/MINTR.sol#L8

```solidity
File: src/modules/PRICE.sol

/// @audit updateMovingAverage(), getCurrentPrice(), getLastPrice(), getMovingAverage(), initialize(), changeMovingAverageDuration(), changeObservationFrequency()
22:   contract OlympusPrice is Module {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L22

```solidity
File: src/modules/RANGE.sol

/// @audit updateCapacity(), updatePrices(), regenerate(), updateMarket(), setSpreads(), setThresholdFactor(), range(), capacity(), active(), price(), spread(), market(), lastActive()
16:   contract OlympusRange is Module {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L16

```solidity
File: src/modules/TRSRY.sol

/// @audit getReserveBalance(), setApprovalFor(), withdrawReserves(), getLoan(), repayLoan(), setDebt()
17:   contract OlympusTreasury is Module, ReentrancyGuard {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L17

```solidity
File: src/modules/VOTES.sol

/// @audit mintTo(), burnFrom()
11:   contract OlympusVotes is Module, ERC20 {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/VOTES.sol#L11

```solidity
File: src/policies/BondCallback.sol

/// @audit batchToTreasury(), setOperator()
17:   contract BondCallback is Policy, ReentrancyGuard, IBondCallback {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L17

```solidity
File: src/policies/Governance.sol

/// @audit getMetadata(), getActiveProposal(), submitProposal(), endorseProposal(), activateProposal(), vote(), executeProposal(), reclaimVotes()
51:   contract OlympusGovernance is Policy {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L51

```solidity
File: src/policies/Heart.sol

/// @audit beat(), frequency(), resetBeat(), toggleBeat(), setRewardTokenAndAmount(), withdrawUnspentRewards()
21:   contract OlympusHeart is IHeart, Policy, ReentrancyGuard {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L21

```solidity
File: src/policies/interfaces/IHeart.sol

/// @audit beat(), frequency(), resetBeat(), toggleBeat(), setRewardTokenAndAmount(), withdrawUnspentRewards()
6:    interface IHeart {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IHeart.sol#L6

```solidity
File: src/policies/interfaces/IOperator.sol

/// @audit operate(), swap(), getAmountOut(), setSpreads(), setThresholdFactor(), setCushionFactor(), setCushionParams(), setReserveFactor(), setRegenParams(), setBondContracts(), initialize(), regenerate(), toggleActive(), fullCapacity(), status(), config()
8:    interface IOperator {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IOperator.sol#L8

```solidity
File: src/policies/Operator.sol

/// @audit bondPurchase(), setSpreads(), setThresholdFactor(), setCushionFactor(), setCushionParams(), setReserveFactor(), setRegenParams(), setBondContracts(), initialize(), regenerate(), toggleActive(), getAmountOut()
30:   contract Operator is IOperator, Policy, ReentrancyGuard {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L30

```solidity
File: src/policies/PriceConfig.sol

/// @audit initialize(), changeMovingAverageDuration(), changeObservationFrequency()
7:    contract OlympusPriceConfig is Policy {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L7

```solidity
File: src/policies/TreasuryCustodian.sol

/// @audit grantApproval(), revokePolicyApprovals(), increaseDebt(), decreaseDebt()
15:   contract TreasuryCustodian is Policy {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L15

```solidity
File: src/policies/VoterRegistration.sol

/// @audit issueVotesTo(), revokeVotesFrom()
9:    contract VoterRegistration is Policy {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/VoterRegistration.sol#L9

### [G&#x2011;15]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There are 11 instances of this issue:*
```solidity
File: src/Kernel.sol

113:      bool public isActive;

181:      mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions;

194:      mapping(address => mapping(Role => bool)) public hasRole;

197:      mapping(Role => bool) public isRole;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L113

```solidity
File: src/modules/PRICE.sol

62:       bool public initialized;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L62

```solidity
File: src/policies/BondCallback.sol

24:       mapping(address => mapping(uint256 => bool)) public approvedMarkets;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L24

```solidity
File: src/policies/Governance.sol

105:      mapping(uint256 => bool) public proposalHasBeenActivated;

117:      mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L105

```solidity
File: src/policies/Heart.sol

33:       bool public active;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L33

```solidity
File: src/policies/Operator.sol

63:       bool public initialized;

66:       bool public active;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L63

### [G&#x2011;16]  Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 3 instances of this issue:*
```solidity
File: src/interfaces/IBondCallback.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/interfaces/IBondCallback.sol#L2

```solidity
File: src/policies/interfaces/IHeart.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IHeart.sol#L2

```solidity
File: src/policies/interfaces/IOperator.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/interfaces/IOperator.sol#L2

### [G&#x2011;17]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 7 instances of this issue:*
```solidity
File: src/policies/Operator.sol

488:              decimals++;

670:                  _status.low.count++;

675:                  _status.low.count--;

686:                  _status.high.count++;

691:                  _status.high.count--;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L488

```solidity
File: src/utils/KernelUtils.sol

49:               i++;

64:               i++;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L49

### [G&#x2011;18]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 11 instances of this issue:*
```solidity
File: src/modules/PRICE.sol

59:       uint8 public constant decimals = 18;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L59

```solidity
File: src/modules/RANGE.sol

65:       uint256 public constant FACTOR_SCALE = 1e4;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L65

```solidity
File: src/policies/Governance.sol

121:      uint256 public constant SUBMISSION_REQUIREMENT = 100;

124:      uint256 public constant ACTIVATION_DEADLINE = 2 weeks;

127:      uint256 public constant GRACE_PERIOD = 1 weeks;

130:      uint256 public constant ENDORSEMENT_THRESHOLD = 20;

133:      uint256 public constant EXECUTION_THRESHOLD = 33;

137:      uint256 public constant EXECUTION_TIMELOCK = 3 days;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L121

```solidity
File: src/policies/Operator.sol

83:       uint8 public immutable ohmDecimals;

86:       uint8 public immutable reserveDecimals;

89:       uint32 public constant FACTOR_SCALE = 1e4;

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L83

### [G&#x2011;19]  Don't compare boolean expressions to boolean literals
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

*There are 2 instances of this issue:*
```solidity
File: src/policies/Governance.sol

223:          if (proposalHasBeenActivated[proposalId_] == true) {

306:          if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L223

### [G&#x2011;20]  Division by two should use bit shifting
`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two

*There are 2 instances of this issue:*
```solidity
File: src/policies/Operator.sol

372:              int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);

427:              int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L372

### [G&#x2011;21]  Superfluous event fields
`block.timestamp` and `block.number` are added to event information by default so adding them manually wastes gas

*There are 7 instances of this issue:*
```solidity
File: src/modules/PRICE.sol

26:       event NewObservation(uint256 timestamp_, uint256 price_, uint256 movingAverage_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L26

```solidity
File: src/modules/RANGE.sol

20:       event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);

21:       event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);

22:       event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);

23:       event CushionDown(bool high_, uint256 timestamp_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L20

```solidity
File: src/policies/Governance.sol

88:       event ProposalActivated(uint256 proposalId, uint256 timestamp);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L88

```solidity
File: src/policies/Heart.sol

28:       event Beat(uint256 timestamp_);

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L28

### [G&#x2011;22]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 36 instances of this issue:*
```solidity
File: src/Kernel.sol

76:       function changeKernel(Kernel newKernel_) external onlyKernel {

105:      function INIT() external virtual onlyKernel {}

126:      function setActiveStatus(bool activate_) external onlyKernel {

235:      function executeAction(Actions action_, address target_) external onlyExecutor {

439:      function grantRole(Role role_, address addr_) public onlyAdmin {

451:      function revokeRole(Role role_, address addr_) public onlyAdmin {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L76

```solidity
File: src/policies/BondCallback.sol

61        function requestPermissions()
62            external
63            view
64            override
65            onlyKernel
66:           returns (Permissions[] memory requests)

83        function whitelist(address teller_, uint256 id_)
84            external
85            override
86:           onlyRole("callback_whitelist")

152:      function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {

190:      function setOperator(Operator operator_) external onlyRole("callback_admin") {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L61-L66

```solidity
File: src/policies/Governance.sol

70        function requestPermissions()
71            external
72            view
73            override
74            onlyKernel
75:           returns (Permissions[] memory requests)

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L70-L75

```solidity
File: src/policies/Heart.sol

130:      function resetBeat() external onlyRole("heart_admin") {

135:      function toggleBeat() external onlyRole("heart_admin") {

140       function setRewardTokenAndAmount(ERC20 token_, uint256 reward_)
141           external
142:          onlyRole("heart_admin")

150:      function withdrawUnspentRewards(ERC20 token_) external onlyRole("heart_admin") {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L130

```solidity
File: src/policies/Operator.sol

195:      function operate() external override onlyWhileActive onlyRole("operator_operate") {

272       function swap(
273           ERC20 tokenIn_,
274           uint256 amountIn_,
275           uint256 minAmountOut_
276:      ) external override onlyWhileActive nonReentrant returns (uint256 amountOut) {

346       function bondPurchase(uint256 id_, uint256 amountOut_)
347           external
348           onlyWhileActive
349:          onlyRole("operator_reporter")

498       function setSpreads(uint256 cushionSpread_, uint256 wallSpread_)
499           external
500:          onlyRole("operator_policy")

510:      function setThresholdFactor(uint256 thresholdFactor_) external onlyRole("operator_policy") {

516:      function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {

527       function setCushionParams(
528           uint32 duration_,
529           uint32 debtBuffer_,
530           uint32 depositInterval_
531:      ) external onlyRole("operator_policy") {

548:      function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {

559       function setRegenParams(
560           uint32 wait_,
561           uint32 threshold_,
562           uint32 observe_
563:      ) external onlyRole("operator_policy") {

586       function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_)
587           external
588:          onlyRole("operator_admin")

598:      function initialize() external onlyRole("operator_admin") {

618:      function regenerate(bool high_) external onlyRole("operator_admin") {

624:      function toggleActive() external onlyRole("operator_admin") {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L195

```solidity
File: src/policies/PriceConfig.sol

45        function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
46            external
47:           onlyRole("price_admin")

58        function changeMovingAverageDuration(uint48 movingAverageDuration_)
59            external
60:           onlyRole("price_admin")

69        function changeObservationFrequency(uint48 observationFrequency_)
70            external
71:           onlyRole("price_admin")

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/PriceConfig.sol#L45-L47

```solidity
File: src/policies/TreasuryCustodian.sol

42        function grantApproval(
43            address for_,
44            ERC20 token_,
45            uint256 amount_
46:       ) external onlyRole("custodian") {

71        function increaseDebt(
72            ERC20 token_,
73            address debtor_,
74            uint256 amount_
75:       ) external onlyRole("custodian") {

80        function decreaseDebt(
81            ERC20 token_,
82            address debtor_,
83            uint256 amount_
84:       ) external onlyRole("custodian") {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L42-L46

```solidity
File: src/policies/VoterRegistration.sol

45:       function issueVotesTo(address wallet_, uint256 amount_) external onlyRole("voter_admin") {

53:       function revokeVotesFrom(address wallet_, uint256 amount_) external onlyRole("voter_admin") {

```
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/VoterRegistration.sol#L45

