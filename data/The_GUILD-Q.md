# Findings1
**Missing Event Emission**

**Description:**
...In the KernelAdapter contract (Kernel.sol), a critical function that changes state misses emission of event. This function changes the address of the Kernel. This event emission is paramount to aid ease of tracking or follow up on state variable changes. Function name is changeKernel(). 

< https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L76>

**Remediation:**
...When the changeKernel function is triggered, emitting an event that tracks the former and new kernel addresses will do immense good for the contract in terms of data simulation. 

# Findings2

**Missing Address(0) check**

# Description: 
...the **bondcallback.sol** constructor set addresses of the token, aggregator and ohm without checking the valid address passed as the argument which pose a minor bug of not been able to change the address to original address if such address is not a valid address.

<https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol#L38>


# Remediation:
 the constructor logic should include an **address(0)** check to confirm a valid address type being passed as argument.

# Findings3
**Missing Event Emission**

 **Description:**
... in the bondcallback.sol, for easy tracking and filtering of some elements event emit is paramount and which is missing in all the functions that made state change to the contracts

**Recommendation**
Events should be emitted where there is state change made in any function for easy simulation of data in the contracts












