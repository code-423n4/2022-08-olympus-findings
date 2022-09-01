## Use ++i instead of i++
On these lines
[Line 225](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L225)
[Line 488](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L488)
[Line 670](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L670)
[Line 686](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L686)
you should use the pre-increment (++i) instead of post-increment (i++) because pre-increment is more gas efficient


