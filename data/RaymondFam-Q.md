## Unreachable code line
`VOTES.sol` [Line 45-48](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L45-L48)
The return value is unreachable because the previous line of code would revert preventing the function from returning true. 
The function should be refactored as follows:
```
    function transfer(address to_, uint256 amount_) public pure override {
        revert VOTES_TransferDisabled();
    }
```
## Typo mistake
numbe is missing an "r"
`PRICE.sol` [Line 126](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L126)

## Unnecessary state variable
`PRICE.sol` [Line 58-59](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L58-L59)
`PRICE.sol` [Line 82-87](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L82-L87)
`PRICE.sol` [Line 89-91](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L89-L91)

`ohmEthDecimals` is already 18 decimals as could be vividly verified on Chainlink Data Feeds. Line 89 can be omitted since exponent is always equal to `reserveEthDecimals` since `decimals - ohmEthDecimals` is always equal to zero. As such, Line 89-91 could be rewritten as follows:
```
 if (reserveEthDecimals > 38) revert Price_InvalidParams();
        _scaleFactor = 10**exponent;
```
With that said, variable `decimals` at line 59 can be deleted. Likewise, variable `ohmEthDecimals`  at line 84 is also deemed removable.