# Check is not present for the amount given in `repayLoan` is less than or equal to debt

File: src/modules/TRSRY.sol: 105

One could pass a greater amount to the function `repayLoan` and thus get more subtracted from the `reserveDebt` and `totalDebt`.

(https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L105)

**Mitigation:**
Check if the amount specified is <= the debt of the address for that particular token.

