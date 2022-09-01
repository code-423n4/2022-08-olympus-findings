#1 Missing natspec comment for activate

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L125

A function has a natspec comment to explain utility about function or parameter but natspec comment activate_ is missing.
So i suggest to add natspec comment for parameter activate_. 

#2 Missing indexed field

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L203

Each event should use three indexed fields if there are three or more fields

#3 File Missing natspec

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L234

A function has a natspec comment to explain utility about function or parameter. So add natspec comment to increase readability 

#4 File missing natspec comment

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L10-L67

A function has a natspec comment to explain utility about function or parameter. So add natspec comment to increase readability 

#5 Missing check address(0)

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L64

To avoid zero address. Wes suggest to add simple check withdraw address in the function.

#6 Address in constructor was missing check

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/MINTR.sol#L15

constructor have params address, so to avoid vulnerability we suggest to consider add simple check address(0) for the params

#7 Mint missing check for address (0)

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/MINTR.sol#L33

Add requirement checkk for address because the adrress cannot be the zero address. it's better to add emit for increase creadibility.

#8 Burn missing check for amount and address

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/MINTR.sol#L37

Add requirement check for address and amount  because the adrress cannot be the zero address. and the amount must greater than zero. it's better to add emit for increase creadibility.

#9 Must be immutable

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L50-L53

the state observationFrequency and movingAverageDuration can't be initialize by constructor. the constructor parameter mention state observationFrequency and movingAverageDuration to initialize. so i suggest to add immutable in observationFrequency and movingAverageDuration.

#10 Ohm must be immutable

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L32

the state ohm can't be initialize by constructor. the constructor parameter mention state ohm to initialize. so i suggest to add immutable in ohm.

#11 Safeapprove is deprecated

Deprecated https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45 
in favor of safeIncreaseAllowance() and safeDecreaseAllowance()

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L57

#12 Reward and rewardToken must be immutable

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L39-L42

the state reward and rewardToken can't be initialize by constructor. the constructor parameter mention state reward and rewardToken to initialize. so i suggest to add immutable in reward and rewardToken.

#13 Missing indexed field for voter

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L87-L89

Each event should use indexed fields if there have any important param. add indexed in voter.