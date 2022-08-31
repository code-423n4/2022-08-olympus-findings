# Gas Optimization

## 📋 Executive Summary
The attention to gas savings throughout the project is excellent.
The use of custom-error and the using the latest version were so impressive.

There are **9** gas optimization tips identified.
I would be happy to help you with your project.

|  | Gas Saved | Instances | Total |  
| --- | --- | --- | --- | 
| G-1 | 8 | 3 | 24 |  
| G-2 | ? | 6 |  |  
| G-3 | 2 | 2 | 4 |  
| G-4 | ? | 3 |  |  
| G-5 | ? | 12 |  |  
| G-6 | 20 | 34 | 680 |  
| G-7 | 25 | 7 | 175 |  
| G-8 | 35 | 17 | 595 |  
| G-9 | 80 | 28 | 2240 |  



## ✅ G-1: Don't Initialize Variables with Default Value

### 📝 Description

Uninitialized variables are assigned with the type's default value. Explicitly initializing a variable with it's default value costs unnecessary gas. Not overwriting the default for [stack variables](https://gist.github.com/IllIllI000/e075d189c1b23dce256cd166e28f3397) saves 8 gas. Storage and memory variables have larger savings

### 💡 Recommendation

Delete useless variable declarations to save gas.

### 🔍 Findings:

`2022-08-olympus/blob/main/src/Kernel.sol#L397` [for (uint256 i = 0; i < reqLength; ) {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L397)

`2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L43` [for (uint256 i = 0; i < 5; ) {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L43)

`2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L58` [for (uint256 i = 0; i < 32; ) {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L58)

## ✅ G-2: Use `uint256` instead of `bool`

### 📝 Description

Booleans are more expensive than uint256 or any type that takes up a full word because each writes operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

### 💡 Recommendation

You should change from bool to uint256 to save gas

### 🔍 Findings:

`2022-08-olympus/blob/main/src/Kernel.sol#L113` [bool public isActive;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L113)

`2022-08-olympus/blob/main/src/modules/PRICE.sol#L62` [bool public initialized;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L62)

`2022-08-olympus/blob/main/src/policies/Heart.sol#L33` [bool public active;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L33)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L63` [bool public initialized;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L63)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L66` [bool public active;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L66)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L735` [bool sideActive = RANGE.active(high\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L735)

## ✅ G-3: Use Shift Right/Left instead of Division/Multiplication if possible

### 📝 Description

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left.
While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas.
Furthermore, Solidity's division operation also includes division-by-0 prevention which is bypassed using shifting.

### 💡 Recommendation

You should change multiplication and division by powers of two to bit shift.

### 🔍 Findings:

`2022-08-olympus/blob/main/src/policies/Operator.sol#L372` [int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L372)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L427` [int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L427)

## ✅ G-4: Solidity Version should be the latest

### 📝 Description

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

### 💡 Recommendation

You should consider with your team members whether you should choose the latest version.
The latest version has its advantages, but also it has disadvantages, such as the possibility of unknown bugs.

### 🔍 Findings:

`2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L2` [pragma solidity >=0.8.0;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondCallback.sol#L2)

`2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#L2` [pragma solidity >=0.8.0;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IHeart.sol#L2)

`2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L2` [pragma solidity >=0.8.0;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L2)

## ✅ G-5: Empty blocks should be removed or emit something

### 📝 Description

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### 💡 Recommendation

Empty blocks should be removed or emit something (The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### 🔍 Findings:

`2022-08-olympus/blob/main/src/Kernel.sol#L85` [constructor(Kernel kernel*) KernelAdapter(kernel*) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L85)

`2022-08-olympus/blob/main/src/Kernel.sol#L95` [function KEYCODE() public pure virtual returns (Keycode) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L95)

`2022-08-olympus/blob/main/src/Kernel.sol#L100` [function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L100)

`2022-08-olympus/blob/main/src/Kernel.sol#L105` [function INIT() external virtual onlyKernel {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L105)

`2022-08-olympus/blob/main/src/Kernel.sol#L115` [constructor(Kernel kernel*) KernelAdapter(kernel*) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L115)

`2022-08-olympus/blob/main/src/Kernel.sol#L143` [function requestPermissions() external view virtual returns (Permissions[] memory requests) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L143)

`2022-08-olympus/blob/main/src/modules/INSTR.sol#L20` [constructor(Kernel kernel*) Module(kernel*) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L20)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L45` [constructor(Kernel kernel*) Module(kernel*) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L45)

`2022-08-olympus/blob/main/src/policies/Governance.sol#L59` [constructor(Kernel kernel*) Policy(kernel*) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L59)

`2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L15` [constructor(Kernel kernel*) Policy(kernel*) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L15)

`2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L24` [constructor(Kernel kernel*) Policy(kernel*) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L24)

`2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L16` [constructor(Kernel kernel*) Policy(kernel*) {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L16)

## ✅ G-6: Functions guaranteed to revert when called by normal users can be marked payable

### 📝 Description

See [this issue](https://github.com/code-423n4/2022-06-putty-findings/issues/59)
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
Saves 2400 gas per instance in deploying contract.
Saves about 20 gas per instance if using assembly to execute the function.

### 💡 Recommendation

You should add the keyword `payable` to those functions.

### 🔍 Findings:

`2022-08-olympus/blob/main/src/Kernel.sol#L76` [function changeKernel(Kernel newKernel\_) external onlyKernel {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L76)

`2022-08-olympus/blob/main/src/Kernel.sol#L105` [function INIT() external virtual onlyKernel {}](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L105)

`2022-08-olympus/blob/main/src/Kernel.sol#L126` [function setActiveStatus(bool activate\_) external onlyKernel {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L126)

`2022-08-olympus/blob/main/src/Kernel.sol#L235` [function executeAction(Actions action*, address target*) external onlyExecutor {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L235)

`2022-08-olympus/blob/main/src/Kernel.sol#L439` [function grantRole(Role role*, address addr*) public onlyAdmin {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439)

`2022-08-olympus/blob/main/src/Kernel.sol#L451` [function revokeRole(Role role*, address addr*) public onlyAdmin {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L451)

`2022-08-olympus/blob/main/src/policies/BondCallback.sol#L86` [onlyRole("callback_whitelist")](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L86)

`2022-08-olympus/blob/main/src/policies/BondCallback.sol#L152` [function batchToTreasury(ERC20[] memory tokens\_) external onlyRole("callback_admin") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L152)

`2022-08-olympus/blob/main/src/policies/BondCallback.sol#L190` [function setOperator(Operator operator\_) external onlyRole("callback_admin") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L190)

`2022-08-olympus/blob/main/src/policies/Heart.sol#L130` [function resetBeat() external onlyRole("heart_admin") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L130)

`2022-08-olympus/blob/main/src/policies/Heart.sol#L135` [function toggleBeat() external onlyRole("heart_admin") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L135)

`2022-08-olympus/blob/main/src/policies/Heart.sol#L142` [onlyRole("heart_admin")](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L142)

`2022-08-olympus/blob/main/src/policies/Heart.sol#L150` [function withdrawUnspentRewards(ERC20 token\_) external onlyRole("heart_admin") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L150)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L195` [function operate() external override onlyWhileActive onlyRole("operator_operate") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L195)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L276` [) external override onlyWhileActive nonReentrant returns (uint256 amountOut) {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L276)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L349` [onlyRole("operator_reporter")](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L349)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L500` [onlyRole("operator_policy")](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L500)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L510` [function setThresholdFactor(uint256 thresholdFactor\_) external onlyRole("operator_policy") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L510)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L516` [function setCushionFactor(uint32 cushionFactor\_) external onlyRole("operator_policy") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L516)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L531` [) external onlyRole("operator_policy") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L531)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L548` [function setReserveFactor(uint32 reserveFactor\_) external onlyRole("operator_policy") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L548)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L563` [) external onlyRole("operator_policy") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L563)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L588` [onlyRole("operator_admin")](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L588)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L598` [function initialize() external onlyRole("operator_admin") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L598)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L618` [function regenerate(bool high\_) external onlyRole("operator_admin") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L618)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L624` [function toggleActive() external onlyRole("operator_admin") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L624)

`2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L47` [onlyRole("price_admin")](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L47)

`2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L60` [onlyRole("price_admin")](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L60)

`2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L71` [onlyRole("price_admin")](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L71)

`2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L46` [) external onlyRole("custodian") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L46)

`2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L75` [) external onlyRole("custodian") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L75)

`2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L84` [) external onlyRole("custodian") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L84)

`2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L45` [function issueVotesTo(address wallet*, uint256 amount*) external onlyRole("voter_admin") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L45)

`2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L53` [function revokeVotesFrom(address wallet*, uint256 amount*) external onlyRole("voter_admin") {](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L53)

## ✅ G-7: Use `++i` instead of `i++`

### 📝 Description

You can get cheaper for loops (at least 25 gas, however, can be up to 80 gas under certain conditions), by rewriting
The post-increment operation (i++) boils down to saving the original value of i, incrementing it and saving that to a temporary place in memory, and then returning the original value; only after that value is returned is the value of i actually updated (4 operations).On the other hand, in pre-increment doesn't need to store temporary(2 operations) so, the pre-increment operation uses fewer opcodes and is thus more gas efficient.

### 💡 Recommendation

You should change from i++ to ++i.

### 🔍 Findings:

`2022-08-olympus/blob/main/src/policies/Operator.sol#L488` [decimals++;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L488)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L670` [\_status.low.count++;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L670)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L675` [\_status.low.count--;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L675)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L686` [\_status.high.count++;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L686)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L691` [\_status.high.count--;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L691)

`2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49` [i++;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49)

`2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64` [i++;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64)

## ✅ G-8: Use x=x+y instad of x+=y

### 📝 Description

You can save about 35 gas per instance if you change from `x+=y` to `x=x+y`
This works equally well for subtraction, multiplication and division.

### 💡 Recommendation

You should change from `x+=y` to `x=x+y`.

### 🔍 Findings:

`2022-08-olympus/blob/main/src/modules/PRICE.sol#L136` [\_movingAverage += (currentPrice - earliestPrice) / numObs;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136)

`2022-08-olympus/blob/main/src/modules/PRICE.sol#L138` [\_movingAverage -= (earliestPrice - currentPrice) / numObs;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L138)

`2022-08-olympus/blob/main/src/modules/PRICE.sol#L222` [total += startObservations\_[i];](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L222)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L96` [reserveDebt[token*][msg.sender] += amount*;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L96)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L97` [totalDebt[token*] += amount*;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L97)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L115` [reserveDebt[token\_][msg.sender] -= received;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L115)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L116` [totalDebt[token\_] -= received;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L116)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131` [if (oldDebt < amount*) totalDebt[token*] += amount\_ - oldDebt;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L132` [else totalDebt[token*] -= oldDebt - amount*;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L132)

`2022-08-olympus/blob/main/src/modules/VOTES.sol#L56` [balanceOf[from*] -= amount*;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L56)

`2022-08-olympus/blob/main/src/modules/VOTES.sol#L58` [balanceOf[to*] += amount*;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L58)

`2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143` [_amountsPerMarket[id_][0] += inputAmount\_;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143)

`2022-08-olympus/blob/main/src/policies/BondCallback.sol#L144` [_amountsPerMarket[id_][1] += outputAmount\_;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L144)

`2022-08-olympus/blob/main/src/policies/Governance.sol#L194` [totalEndorsementsForProposal[proposalId\_] -= previousEndorsement;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L194)

`2022-08-olympus/blob/main/src/policies/Governance.sol#L198` [totalEndorsementsForProposal[proposalId\_] += userVotes;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L198)

`2022-08-olympus/blob/main/src/policies/Governance.sol#L252` [yesVotesForProposal[activeProposal.proposalId] += userVotes;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L252)

`2022-08-olympus/blob/main/src/policies/Governance.sol#L254` [noVotesForProposal[activeProposal.proposalId] += userVotes;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L254)

`2022-08-olympus/blob/main/src/policies/Heart.sol#L103` [lastBeat += frequency();](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103)

## ✅ G-9: Use `indexed` for uint, bool, and address

### 📝 Description

Using the indexed keyword for value types such as uint, bool, and address saves gas costs, as seen in the example below. However, this is only the case for value types, whereas indexing bytes and strings are more expensive than their unindexed version.
If you add `indexed` keyword, you can save gas at least 80 gas in emitting event. Please read [this](https://gist.github.com/Tomosuke0930/9bf61e01a8c3e214d95a9b84dcb41d97) document.
Also indexed keyword has more merits.
It can be useful to have a way to monitor the contract's activity after it was deployed. One way to accomplish this is to look at all transactions of the contract, however that may be insufficient, as message calls between contracts are not recorded in the blockchain. Moreover, it shows only the input parameters, not the actual changes being made to the state. Also events could be used to trigger functions in the user interface.

### 💡 Recommendation

Up to three `indexed` can be used per event and should be added.

### 🔍 Findings:
`2022-08-olympus/blob/main/src/modules/INSTR.sol#L11` [event InstructionsStored(uint256 instructionsId);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L11)

`2022-08-olympus/blob/main/src/modules/PRICE.sol#L26` [event NewObservation(uint256 timestamp_, uint256 price_, uint256 movingAverage\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L26)

`2022-08-olympus/blob/main/src/modules/PRICE.sol#L27` [event MovingAverageDurationChanged(uint48 movingAverageDuration\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L27)

`2022-08-olympus/blob/main/src/modules/PRICE.sol#L28` [event ObservationFrequencyChanged(uint48 observationFrequency\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L28)

`2022-08-olympus/blob/main/src/modules/RANGE.sol#L20` [event WallUp(bool high_, uint256 timestamp_, uint256 capacity\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L20)

`2022-08-olympus/blob/main/src/modules/RANGE.sol#L21` [event WallDown(bool high_, uint256 timestamp_, uint256 capacity\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L21)

`2022-08-olympus/blob/main/src/modules/RANGE.sol#L22` [event CushionUp(bool high_, uint256 timestamp_, uint256 capacity\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L22)

`2022-08-olympus/blob/main/src/modules/RANGE.sol#L23` [event CushionDown(bool high_, uint256 timestamp_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L23)

`2022-08-olympus/blob/main/src/modules/RANGE.sol#L24` [event PricesChanged(](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L24)

`2022-08-olympus/blob/main/src/modules/RANGE.sol#L30` [event SpreadsChanged(uint256 cushionSpread_, uint256 wallSpread_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L30)

`2022-08-olympus/blob/main/src/modules/RANGE.sol#L31` [event ThresholdFactorChanged(uint256 thresholdFactor\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L31)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L20` [event ApprovedForWithdrawal(address indexed policy_, ERC20 indexed token_, uint256 amount\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L20)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L27` [event DebtIncurred(ERC20 indexed token_, address indexed policy_, uint256 amount\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L27)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L28` [event DebtRepaid(ERC20 indexed token_, address indexed policy_, uint256 amount\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L28)

`2022-08-olympus/blob/main/src/modules/TRSRY.sol#L29` [event DebtSet(ERC20 indexed token_, address indexed policy_, uint256 amount\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L29)

`2022-08-olympus/blob/main/src/policies/Governance.sol#L86` [event ProposalSubmitted(uint256 proposalId);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L86)

`2022-08-olympus/blob/main/src/policies/Governance.sol#L87` [event ProposalEndorsed(uint256 proposalId, address voter, uint256 amount);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L87)

`2022-08-olympus/blob/main/src/policies/Governance.sol#L88` [event ProposalActivated(uint256 proposalId, uint256 timestamp);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L88)

`2022-08-olympus/blob/main/src/policies/Governance.sol#L89` [event WalletVoted(uint256 proposalId, address voter, bool for\_, uint256 userVotes);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L89)

`2022-08-olympus/blob/main/src/policies/Governance.sol#L90` [event ProposalExecuted(uint256 proposalId);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L90)

`2022-08-olympus/blob/main/src/policies/Heart.sol#L28` [event Beat(uint256 timestamp\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L28)

`2022-08-olympus/blob/main/src/policies/Heart.sol#L29` [event RewardIssued(address to_, uint256 rewardAmount_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L29)

`2022-08-olympus/blob/main/src/policies/Heart.sol#L30` [event RewardUpdated(ERC20 token_, uint256 rewardAmount_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L30)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L45` [event Swap(](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L45)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L51` [event CushionFactorChanged(uint32 cushionFactor\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L51)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L52` [event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L52)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L53` [event ReserveFactorChanged(uint32 reserveFactor\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L53)

`2022-08-olympus/blob/main/src/policies/Operator.sol#L54` [event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe\_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L54)
