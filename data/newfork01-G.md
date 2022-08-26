### description

calldata should be used instead of memory for function parameters saves gas if the function argument is only read.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L393

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205

https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondTeller.sol#L43