## Conflicting import at KernelUtils.sol 

### Lines of Code

KernelUtils file :

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L4

Kernel.sol file :

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L54-L55



### Details

A conflicting import is present at the KernelUtils.sol file, which causes a DeclarationError.


### Fix

The import line in the KernelUtils.sol file (#L4) may be removed.

The declaration of Keycode and Role variables may be done in the KernelUtils.sol file in place of the previously mentioned import line.

The declarations of Keycode and Role variables in the Kernel.sol file (#L54 and #L55) may be removed.