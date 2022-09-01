# [informational] No Module uses the INIT() function which is enstated in Kernel.sol in the Module class.

## Synopsis
In the abstract Module class within Kernel.sol, the INIT() function is declared without any implementation present, with the intention evidently being to implement the respective INIT codes within the Modules themselves. However, none of the present Modules are overriding the INIT function and so, whenever the kernel is installing a new module and the _newModule.INIT() function is being called, no initialisation is happening on the modules.

## Proof of concept

None of the modules are using the INIT function which has been created in Kernel.sol's Module class:
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L105

Used here: 
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L290
And 
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L276

Price.sol has its own initialise function.

## Discussion

The spec notes:
````
    /// @notice Initialization function for the module
    /// @dev    This function is called when the module is installed or upgraded by the kernel.
    /// @dev    MUST BE GATED BY onlyKernel. Used to encompass any initialization or upgrade logic.
````

All of the conditions are technically met, however, nothing gets initialised for any existing module through these initialisations, so in current context it is using deployment gas unnecessarily. 
