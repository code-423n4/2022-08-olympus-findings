**Kernel.sol**
- L236 to L258 - This type of validation structure can be replaced by a switch, where the parameter passed first is action and then each case is the value of the Actions enum.

- L439 - Currently the functionalities of setting the role to an address and creating a new role are coupled. This can generate confusion since unconsciously roles can be created due to mistakes in the name of the role, since that is not really the objective. Therefore, it would be best to have a function to create a new role and another function to grant the role to a specific address.

**TRSRY.sol**
- L20/24/27/28/29/33/36/39/59/66/77/92/105/123/139 - In multiple functions, an ERC20 with the name token_ is requested as input, mostly. But no internal function of this type of token is ever used. This generates extra gas costs, since it could simply be used as an address.

- L33/37 - The Executor can define who mints and burns tokens, this has a very high centralization point, since the address that has access to it, passing the require permissioned, will be able to burn and mint tokens to whoever it wants. Generating a very high risk point.

**VOTES.sol**
- L35/39/51 - The Executor can define who lies, burns and transfers the token of whoever he wants, this has a very high centralization point, since the address that has access to it, pass the require permissioned, can burn, lie and transfer tokens to whoever he wants. Generating a very high risk point.

**RANGE.sol**
- L33 - If the struct that is created has only one element, it would be nicer for it to be just a variable and not a struct.
