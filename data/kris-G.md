## Useless iterator initialization  in the loop

In the function _setPolicyPermissions() contract Kernel ‘uint256 i’ is initializing to 0 what is useless as type uint256 by default equals to 0.

**Recommendation:** Consider removing useless initialization to decrease the gas cost for calling functions

Reference: https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L391

## IF statements order optimization

Contract OlympusInstructions function store() there is a check if the instruction array is not empty (the length is not equal to 0). It makes sense to do it first of all because if there are no instructions there is no point to keep function execution.

**Recommendation:** Consider moving the 48 line (check if the instruction array is not empty) below the 43 line to avoid the unnecessary operation of incrementation and call to storage in the case when the array is empty.

Reference: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L42


## Function visibility optimization 

In contract OlympusInstructions there is the function getInstructions() which is not called anywhere inside the contract, so can be marked as external instead of public.

Reference: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L37

In contract OlympusMinter there are a few functions that are not called inside the contract mintOhm() and burnOhm().

Reference: https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L33

**Recommendation:** Consider changing visibility from public to external for function getInstructions() , mintOhm() and burnOhm().
