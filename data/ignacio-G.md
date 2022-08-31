# 1 Use != 0 instead of > 0 at the above mentioned codes. The variable is uint, so it will not be below 0 so it can just check != 0.
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L247

# 2 <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L97
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103

#  3 ++I COSTS LESS GAS THAN I++
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L670
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L686

# 4 MULTIPLE ADDRESS MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS TO A STRUCT, WHERE APPROPRIATE
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L96  to  # 117 
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L33

# 5  USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.
If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it’s still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L27
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L34
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L154
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L171
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L171
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L798
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L48
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L66
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L152
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L173
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L69
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L81
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L265
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L61
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L75
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L159
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L19
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L27
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L146
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L275
