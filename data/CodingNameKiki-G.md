1. Arithmetics: Unchecking arithmetic operations that cannot underflow/overflow

From Solidity v0.8 onwards, all arithmetic operations come with implicit overflow and underflow checks. In some instances, an overflow/underflow is impossible and gas can be saved by using an unchecked block to remove the implicit checks.

1.The first unchecking is in the getLoan function
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L92

a) There is one arithmetic operation in the function, that can be unchecked showed below, it adds the amount of the loan to the debtor's address. l don't think that this operation can overflow, since the function is permissioned and l doubt the loan of a one person, can get over (2**256 - 1). 
(Before) 96: reserveDebt[token_][msg.sender] += amount_; 
(After) 96: unchecked {  reserveDebt[token_][msg.sender] += amount_; }

2.The second unchecking is in the repayLoan function
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L105

a) There are two arithmetic operations in the function, the first one showed below, it subtract the received amount of loan from the debtor's address. This can operation can be unchecked to save gas, there is no chance for underflow, since the received amount is equal to the debtor's amount and it can't go below zero, on the other hand if the msg.sender doesn't have Debt the function reverts, preventing malicious users to access the function.
(Before) 115: reserveDebt[token_][msg.sender] -= received;
(After) unchecked { reserveDebt[token_][msg.sender] -= received; }

b) The second operation showed below subtract the amount of the repaid loan from the amount of the token total loans among debtors. Since the 
totalDebt is all users loans in a particular token, it can't underflow by subtracting a single debtor loan.
(Below) 116: totalDebt[token_] -= received;
(After) 116: unchecked { totalDebt[token_] -= received; }

3.The third unchecking is in the setDebt function
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/TRSRY.sol#L122

a) There is one arithmetic operation that can be unchecked showed below, in the else statement the total debt of a token is subtracted by (the debtor's old debt subtractted by the amount of the new one), since in the else statement the oldDebt is greater or equal than the amount of the new one and the totalDebt is the debt made by all debtors, it can't underflow.
(Before) 132: else totalDebt[token_] -= oldDebt - amount_;
(After) 132: else unchecked { totalDebt[token_] -= oldDebt - amount_; } 

4.The fourth uncheking is in the constructor
https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L71

a) There is one arithmetic operation that can be unchecked showed below, the _scalefactor equals 10 to the power of the exponent, since in the if statement the exponent can't be greater than the number 38, (10 to the power of 38 isn't greater than 2**256 -1 ) so it can be declared as unchecked, since there is no risk to overflow.
(Before) 91: _scaleFactor = 10**exponent;
(After) 91: unchecked { _scaleFactor = 10**exponent; }

2. There is no need to declare variables with their default value:

1.uint: 0
2.bool: false
3.address: address(0)

src/utils/KernelUtils.sol
(Before) 43: for (uint256 i = 0; i < 5; ) {
(After) 43: for (uint256 i; i < 5; ) {

(Before) 58: for (uint256 i = 0; i < 32; ) {
(After) 58: for (uint256 i; i < 32; ) {

src/Kernel.sol
(Before) 397: for (uint256 i = 0; i < reqLength; ) {
(After) 397: for (uint256 i; i < reqLength; ) {