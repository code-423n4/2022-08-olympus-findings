Table Of Content
================

* [QA REPORT](#qa-report)
        * [Use safeApprove() instead approve()](#use-safeapprove-instead-approve)
        * [Unused success return value](#unused-success-return-value)
        * [Use safeTransfer() instead transfer()](#use-safetransfer-instead-transfer)
        * [Missing two steps verification process](#missing-two-steps-verification-process)
        * [Wrong use of assert](#wrong-use-of-assert)
        * [Avoid floating pragma](#avoid-floating-pragma)
        * [Missing 0 address check at transfer](#missing-0-address-check-at-transfer)
        * [Array access is out of bounds](#array-access-is-out-of-bounds)
        * [Should approve(0) first](#should-approve0-first)
        * [Several functions are declaring named returns but then are using return statements. I suggest choosing only one for readability reasons.](#several-functions-are-declaring-named-returns-but-then-are-using-return-statements-i-suggest-choosing-only-one-for-readability-reasons)
        * [Consider adding constant variables instead of hardcoded strings](#consider-adding-constant-variables-instead-of-hardcoded-strings)
        * [Add event to the following functions](#add-event-to-the-following-functions)
        * [Events not emitted for important state changes](#events-not-emitted-for-important-state-changes)

# QA REPORT

## Use safeApprove() instead approve()
Use openzeppelin safeApprove() method instead of approve() in the following locations.

### Code Instances:
- [Operator.t.sol#L191](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L191)
- [Governance.t.sol#L95](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L95)
- [Operator.t.sol#L198](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L198)
- [Operator.t.sol#L193](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L193)
- [BondCallback.t.sol#L206](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L206)
- [BondCallback.t.sol#L208](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L208)
- [TRSRY.t.sol#L155](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/TRSRY.t.sol#L155)
- [Governance.t.sol#L101](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L101)
- [Governance.t.sol#L103](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L103)

## Unused success return value
The following calls ignores the return value of the called function that might indicate the the call failed.

### Code Instances:
- [Operator.t.sol#L328](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L328)
- [Operator.t.sol#L300](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L300)
- [VOTES.t.sol#L49](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/VOTES.t.sol#L49)
- [Operator.t.sol#L1380](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1380)
- [Operator.t.sol#L342](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L342)
- [Operator.t.sol#L1319](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1319)
- [Operator.t.sol#L997](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L997)
- [Operator.t.sol#L1882](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1882)
- [Operator.t.sol#L314](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L314)
- [Operator.t.sol#L1240](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1240)
- [Operator.t.sol#L1222](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1222)
- [Operator.t.sol#L1299](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1299)
- [Operator.t.sol#L1915](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1915)
- [Operator.t.sol#L1866](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1866)
- [Operator.t.sol#L398](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L398)
- [Operator.t.sol#L1864](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1864)
- [Operator.t.sol#L820](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L820)
- [Operator.t.sol#L1154](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1154)
- [RANGE.t.sol#L122](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L122)
- [Operator.t.sol#L1202](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1202)
- [Heart.t.sol#L247](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Heart.t.sol#L247)
- [RANGE.t.sol#L137](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L137)
- [Operator.t.sol#L1278](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1278)
- [Operator.t.sol#L1246](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1246)
- [Governance.sol#L258](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L258)
- [Operator.t.sol#L1069](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1069)
- [Operator.t.sol#L438](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L438)
- [Operator.t.sol#L1992](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1992)
- [Governance.sol#L311](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L311)
- [Operator.t.sol#L1404](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1404)
- [Operator.t.sol#L1111](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1111)
- [Operator.t.sol#L399](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L399)
- [Operator.t.sol#L927](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L927)
- [Operator.t.sol#L1916](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1916)
- [Operator.t.sol#L933](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L933)

## Use safeTransfer() instead transfer()
Use openzeppelin safeTransfer() method instead of transfer() in the following locations.

### Code Instances:
- [VOTES.t.sol#L73](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/VOTES.t.sol#L73)
- [VOTES.t.sol#L34](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/VOTES.t.sol#L34)
- [Governance.sol#L78](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L78)
- [VOTES.t.sol#L49](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/VOTES.t.sol#L49)

## Missing two steps verification process
The process of transferring ownership is dangerous since typing the wrong address can lead to severe implications. It is better to have to steps verification process with set and claim functions to decrease the chances of human error. Consider changing to two steps verification process of transferring privileges. Human mistakes can happen.

For instance, [Kernel.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol)

## Wrong use of assert
You should use if-revert or require statements instead of assertions in production.

### Code Instances:
- [BondCallback.t.sol#L476](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L476)
- [BondCallback.t.sol#L483](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L483)

## Avoid floating pragma
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively. (SWC-103)

### Code Instances:
- [BondCallback.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol)
- [VOTES.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/VOTES.t.sol)
- [PriceConfig.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/PriceConfig.t.sol)
- [RANGE.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol)
- [TreasuryCustodian.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/TreasuryCustodian.t.sol)
- [PRICE.t.sol](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol)

## Missing 0 address check at transfer
Some contracts does not support 0 transfer, then the transaction will revert with no explanation. We recommend to add a require statement that the amount is not 0.

### Code Instances:
- [VOTES.sol#L60](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L60)
- [Operator.sol#L329](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L329)
- [TRSRY.sol#L98](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L98)
- [TRSRY.sol#L81](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L81)

## Array access is out of bounds
There is no check for the access to be in the array bounds.

### Code Instances:
- [Kernel.sol#L325](https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L325)
- [RANGE.sol#L81](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L81)
- [PRICE.sol#L183](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L183)

## Should approve(0) first
Some tokens (like USDT L199) do not work when changing the allowance from an existing non-zero allowance value.
They must first be approved by zero and then the actual allowance must be approved.

### Code Instances:
- [TRSRY.t.sol#L130](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/TRSRY.t.sol#L130)
- [BondCallback.t.sol#L213](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L213)
- [Operator.sol#L166](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L166)
- [Operator.t.sol#L193](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L193)
- [TRSRY.t.sol#L100](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/TRSRY.t.sol#L100)
- [Operator.t.sol#L196](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L196)
- [Governance.t.sol#L95](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L95)
- [BondCallback.t.sol#L211](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L211)
- [Governance.t.sol#L97](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L97)
- [BondCallback.t.sol#L476](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L476)
- [Operator.t.sol#L198](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L198)
- [TRSRY.t.sol#L132](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/TRSRY.t.sol#L132)

## Several functions are declaring named returns but then are using return statements. I suggest choosing only one for readability reasons.
Using both named returns and a return statement isn't necessary. Removing one of those can improve code clarity.

### Code Instances:
- [BondCallback.t.sol#L249](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L249)
- [VOTES.sol#L27](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L27)
- [PRICE.sol#L113](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L113)
- [BondCallback.sol#L178](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L178)

## Consider adding constant variables instead of hardcoded strings
A good practice is to use constant variables instead of hardcoded strings in the code.

### Code Instances:
- [Heart.t.sol#L259](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Heart.t.sol#L259)
- [Governance.t.sol#L156](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L156)
- [Kernel.t.sol#L165](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L165)
- [Operator.t.sol#L407](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L407)
- [Operator.t.sol#L172](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L172)
- [Operator.sol#L157](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L157)
- [PriceConfig.sol#L19](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L19)
- [PRICE.t.sol#L37](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol#L37)
- [Kernel.t.sol#L341](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L341)
- [TreasuryCustodian.sol#L28](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L28)
- [PRICE.sol#L108](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L108)
- [Operator.t.sol#L1727](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1727)
- [PRICE.t.sol#L485](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol#L485)
- [Deploy.sol#L270](https://github.com/code-423n4/2022-08-olympus/tree/main/src/scripts/Deploy.sol#L270)
- [TRSRY.t.sol#L49](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/TRSRY.t.sol#L49)
- [RANGE.t.sol#L48](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L48)
- [Operator.t.sol#L1954](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1954)
- [RANGE.sol#L110](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L110)
- [Kernel.t.sol#L154](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L154)
- [MINTR.sol#L20](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L20)
- [Kernel.t.sol#L140](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L140)
- [Kernel.t.sol#L86](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L86)
- [Heart.t.sol#L162](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Heart.t.sol#L162)
- [VoterRegistration.t.sol#L55](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/VoterRegistration.t.sol#L55)
- [BondCallback.t.sol#L177](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L177)
- [Operator.sol#L158](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L158)
- [Kernel.t.sol#L212](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L212)
- [BondCallback.sol#L49](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L49)
- [BondCallback.t.sol#L105](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L105)
- [Operator.t.sol#L165](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L165)
- [Kernel.t.sol#L129](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L129)
- [Operator.t.sol#L1537](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1537)
- [BondCallback.sol#L50](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L50)
- [Kernel.t.sol#L110](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L110)
- [Kernel.t.sol#L149](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L149)
- [VoterRegistration.t.sol#L47](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/VoterRegistration.t.sol#L47)
- [BondCallback.t.sol#L183](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L183)
- [Heart.sol#L70](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L70)
- [BondCallback.t.sol#L488](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L488)
- [Governance.t.sol#L138](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L138)
- [Heart.t.sol#L300](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Heart.t.sol#L300)
- [TreasuryCustodian.t.sol#L54](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/TreasuryCustodian.t.sol#L54)
- [Heart.t.sol#L74](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Heart.t.sol#L74)
- [Heart.t.sol#L121](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Heart.t.sol#L121)
- [Operator.t.sol#L2081](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L2081)
- [BondCallback.t.sol#L184](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L184)
- [BondCallback.t.sol#L178](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L178)
- [Heart.t.sol#L120](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Heart.t.sol#L120)
- [Kernel.t.sol#L76](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L76)
- [Kernel.t.sol#L216](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L216)
- [Kernel.t.sol#L358](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L358)
- [Kernel.t.sol#L88](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L88)
- [PRICE.t.sol#L155](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol#L155)
- [Kernel.t.sol#L78](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L78)
- [Operator.t.sol#L1502](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1502)
- [Operator.t.sol#L1686](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1686)
- [RANGE.t.sol#L49](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L49)
- [Governance.sol#L62](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L62)
- [Kernel.t.sol#L243](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L243)
- [Kernel.t.sol#L168](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L168)
- [Operator.t.sol#L2005](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L2005)
- [Deploy.sol#L268](https://github.com/code-423n4/2022-08-olympus/tree/main/src/scripts/Deploy.sol#L268)
- [Operator.t.sol#L1475](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1475)
- [Operator.t.sol#L446](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L446)
- [Kernel.t.sol#L53](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L53)
- [Kernel.t.sol#L356](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L356)
- [Kernel.t.sol#L95](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L95)
- [BondCallback.t.sol#L179](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L179)
- [Operator.t.sol#L173](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L173)
- [Kernel.t.sol#L139](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L139)
- [Kernel.t.sol#L103](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L103)
- [Operator.t.sol#L1794](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Operator.t.sol#L1794)
- [Kernel.t.sol#L68](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L68)
- [Kernel.t.sol#L80](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L80)
- [Kernel.t.sol#L232](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L232)
- [BondCallback.t.sol#L429](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L429)
- [Kernel.t.sol#L280](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L280)
- [Operator.sol#L156](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L156)

## Add event to the following functions


### Code Instances:
- [VoterRegistration.sol#L19](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/VoterRegistration.sol#L19)
- [Governance.t.sol#L46](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L46)
- [RANGE.t.sol#L442](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L442)
- [Governance.sol#L61](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L61)
- [Kernel.t.sol#L135](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L135)
- [Operator.sol#L154](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L154)
- [Kernel.t.sol#L108](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L108)
- [PRICE.sol#L208](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L208)
- [Kernel.t.sol#L309](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L309)
- [Kernel.t.sol#L23](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L23)
- [PRICE.t.sol#L33](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol#L33)
- [PRICE.sol#L183](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L183)
- [RANGE.t.sol#L35](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L35)
- [BondCallback.sol#L42](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L42)
- [RANGE.t.sol#L424](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L424)
- [RANGE.t.sol#L453](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L453)
- [Kernel.t.sol#L94](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L94)
- [PriceConfig.t.sol#L37](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/PriceConfig.t.sol#L37)
- [Heart.sol#L69](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L69)
- [RANGE.t.sol#L433](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L433)
- [Operator.sol#L598](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L598)
- [PriceConfig.sol#L18](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L18)
- [Kernel.t.sol#L326](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L326)
- [RANGE.t.sol#L471](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L471)
- [Deploy.sol#L264](https://github.com/code-423n4/2022-08-olympus/tree/main/src/scripts/Deploy.sol#L264)
- [Kernel.t.sol#L270](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L270)
- [Kernel.t.sol#L62](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L62)

## Events not emitted for important state changes
When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

### Code Instances:
- [RANGE.t.sol#L433](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L433)
- [TreasuryCustodian.sol#L27](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L27)
- [PriceConfig.t.sol#L37](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/PriceConfig.t.sol#L37)
- [Heart.sol#L135](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L135)
- [BondCallback.sol#L42](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L42)
- [RANGE.t.sol#L35](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L35)
- [Kernel.t.sol#L23](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L23)
- [Heart.sol#L130](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L130)
- [Kernel.t.sol#L270](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L270)
- [VOTES.t.sol#L23](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/VOTES.t.sol#L23)
- [RANGE.t.sol#L424](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L424)
- [Deploy.sol#L264](https://github.com/code-423n4/2022-08-olympus/tree/main/src/scripts/Deploy.sol#L264)
- [TreasuryCustodian.t.sol#L26](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/TreasuryCustodian.t.sol#L26)
- [Operator.sol#L154](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L154)
- [PRICE.sol#L208](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L208)
- [Kernel.sol#L217](https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L217)
- [Kernel.t.sol#L94](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L94)
- [MINTR.sol#L15](https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/MINTR.sol#L15)
- [RANGE.t.sol#L462](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L462)
- [Operator.sol#L589](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L589)
- [TRSRY.t.sol#L28](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/TRSRY.t.sol#L28)
- [Kernel.t.sol#L326](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L326)
- [Kernel.t.sol#L309](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L309)
- [PRICE.t.sol#L33](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/PRICE.t.sol#L33)
- [Operator.sol#L598](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L598)
- [BondCallback.t.sol#L80](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/BondCallback.t.sol#L80)
- [Kernel.t.sol#L209](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/Kernel.t.sol#L209)
