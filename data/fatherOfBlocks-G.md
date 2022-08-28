**Kernel.sol**
- L70/88/119/223/229 - Gas can be saved if instead of using a modifier a private view function is used, this would reduce all the costs of validating the access control of an address.

- L397 - When you want to set a variable with its default value, it is less expensive not to set it since it has that default value, this reduces some gas units without losing understanding of the code.

**KernelUtils.sol**
- L43/58 - When you want to set a variable with its default value, it is less expensive not to set it since it has that default value, this reduces some gas units without losing understanding of the code.

- L49/64 - It is less expensive to do the ++i operation than to do i++, without losing understanding of the code.

**TRSRY.sol**
- L131/132 - When previously it is validated that the operation returns a value without overflow/underflow, it can be wrapped with unchecked in order to spend less gas when performing the mathematical operation. What could be unchecked is the operation, not the validation of the if.

**PRICE.sol**
- L6 - The ERC20 class is imported, but it is never used, this generates unnecessary extra gas costs.

- L136/138 - When it is previously validated that the operation returns a value without overflow/underflow, it can be wrapped with unchecked in order to spend less gas when performing the mathematical operation. What could be unchecked is the operation, not the validation of the if.

**BondCallback.sol**
- L120 - It is necessary to validate that outputAmount_ > inputAmount_ so that it does not throw an exception without any underflow message. In addition, the outputAmount_ - inputAmount_ operation can become unchecked so that what has already been validated is not validated.

- L223/306 - Instead of validating "validation == true" or "validation == false" it is much simpler and less expensive to validate "validation" or "!validation".

- L278 - In a for loop instead of consulting the length of the array to be iterated in each iteration, the least expensive thing is to create a variable in memory of the value of array.length

**RANGE.sol**
- L133/145 - It is less expensive in a validation that the less expensive operation is ahead, since it avoids executing the second more expensive validation.

**Heart.sun**
- L26 - an error is created that is not used anywhere, it should be eliminated.


**TreasuryCustodian.sol**
- L11 - an error is created that is not used anywhere, it should be eliminated.


**Operator.sol**
- L188 - The modifier can generate much less gas cost, if instead of a modifier it were a private view function.

- L488/670/675 - It is less expensive to make ++variable than to make variable++, without modifying the understanding of the code.
