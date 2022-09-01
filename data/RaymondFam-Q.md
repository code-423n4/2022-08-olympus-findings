## Unreachable code line
`VOTES.sol` [Line 45-48](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L45-L48)

The return code line is unreachable because the previous line of code would revert preventing the function from returning true, which is understandably done in deliberation. 

The function could be refactored as follows with a good side effect of reducing the contract size:
```
    function transfer(address to_, uint256 amount_) public pure override returns (bool) {
        revert VOTES_TransferDisabled();
    }
```
## Typo mistake
`PRICE.sol` [Line 126](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L126)

numbe is missing an "r"
## Unnecessary memory variables
`PRICE.sol` [Line 58-59](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L58-L59)
`PRICE.sol` [Line 82-91](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L82-L91)

`ohmEthDecimals` is already 18 decimals long as could be vividly verified on Chainlink Data Feeds for OHMv2 / ETH pair. Lines 84 & 89 can be omitted since exponent is always equal to `reserveEthDecimals` considering `decimals - ohmEthDecimals` is going to be zero. As such, Lines 90-91 could be rewritten as follows:
```
 if (reserveEthDecimals > 38) revert Price_InvalidParams();
        _scaleFactor = 10**exponent;
```
Note: Variable `decimals` at line 59 cannot be deleted since it's being called/referenced by Operator.sol.