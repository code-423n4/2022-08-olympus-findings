# Shadowed variables

IMPACT:
  Shadowing local variables is naming conventions found in two or more variables that are similar. Although they do not pose any immediate risk to the contract, incorrect usage of the variables is possible and can cause serious issues if the developer does not pay close attention. [Reference|https://github.com/crytic/slither/wiki/Detector-Documentation#local-variable-shadowing]

ERC20Permit.constructor(string).name (OlympusERC20.sol#844) 
shadows:
	ERC20.name() (OlympusERC20.sol#689-691) (function)

Mitigation:
	rename name to _name

# Requirement violation.

(Https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L300) 

A requirement was violated in a nested call and the call was reverted as  a result. Make sure valid inputs are provided to the nested call (for  instance, via passed arguments).
Reference: https://swcregistry.io/docs/SWC-123




# public functions not called by the contract should be declared external instead
Impact: 
	Contracts are allowed to override their parents' functions and change the visibility from external to public.
	Kernel.grantRole(Role,address) (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L443-L452)
	Kernel.revokeRole(Role,address) (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L455-L462)
	OlympusMinter.mintOhm(address,uint256) (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L33-L35)
	OlympusMinter.burnOhm(address,uint256) (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L37-L39)
	OlympusVotes.transfer(address,uint256) (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L45-48)
	OlympusVotes.transferFrom(address,address,uint256) (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L51-63)
	OlympusInstructions.getInstructions(uint256) (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L37-39)





# MixedCase:

Functions should be written in mixedCase, see Solidity naming conventions.
Functions breaking this convention:

Reference:
https://github.com/code-423n4/2021-11-bootfinance-findings/issues/163

Function OlympusRange.KEYCODE() - (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L110-L112) 
Function OlympusRange.VERSION() (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L115-L117) 
Function OlympusVotes.KEYCODE() (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L22-24) 
Function OlympusVotes.VERSION() (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L27-29) 
Function OlympusInstructions.KEYCODE() (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L23-25) 
Function OlympusInstructions.VERSION() (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L28-30) 
Variable TreasuryCustodian.TRSRY (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/TreasuryCustodian.sol#L20)
Variable Operator._status (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L59) 
Variable Operator._config (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L60) 
Variable Operator.PRICE (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L69) 
Variable Operator.RANGE (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L70) 
Variable Operator.TRSRY (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L71) 
Variable Operator.MINTR (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L72)
Variable OlympusGovernance.INSTR (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L56) 
Variable OlympusGovernance.VOTES (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L57) 
Variable VoterRegistration.VOTES (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/VoterRegistration.sol#L10) 
Variable BondCallback.TRSRY (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L29) 
Variable BondCallback.MINTR (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L30) 
Variable OlympusPriceConfig.PRICE (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/PriceConfig.sol#L11) 
Variable OlympusHeart.PRICE (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L45) 
Variable OlympusHeart._operator (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L48) 
Function Module.KEYCODE() (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L99) 
Function Module.VERSION() (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L104) 
Function Module.INIT() (Https://github.com/code-423n4/2022-08-olympus/tree/main/src/Kernel.sol#L109)
Function OlympusPrice.KEYCODE() (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L108-110) 
Function OlympusPrice.VERSION() (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L113-115) 
Variable OlympusPrice._ohmEthPriceFeed (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L32) 
Variable OlympusPrice._reserveEthPriceFeed (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L33) 
Variable OlympusPrice._movingAverage (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L36) 
Variable OlympusPrice._scaleFactor (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L65) 
Function OlympusTreasury.KEYCODE() (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L47-L49)
Function OlympusTreasury.VERSION() (Https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L51-L53)








