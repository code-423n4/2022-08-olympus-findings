- **Finding**: Constructor parameter are not validated
    **Severity:** QA
    **Description:**
    Constructor parameters are not validated at:
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L77
    To mitigate the risk of misconfiguration, it's recommended to validate `tokens_` and `rangeParams_` parameters
    (the former is used to set immutable variables, which cannot be modified after being set incorrectly in constructor).

- **Finding**: Misconfiguration risk due to usage of arrays to pass function arguments
    **Severity:** QA
    **Description:**
    The usage of arrays when passing multiple arguments to functions can cause misconfiguration issues because deployer/
    user has to ensure the order of array elements is correct when calling a function. It's recommended to use parameters
    structures in these cases:
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L79-L80
        For `tokens_` and `rangeParams_`. Passing tokens in a wrong order might result in an unusable contract.
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L96-L97
        For `tokens_` and `configParams`. Passing tokens in a wrong order might result in an unusable contract. `configParams` contains many values that are hard to read/track in the code since they're referenced by their index in the array, not their name.

    Poor readability of array arguments forced developers to add the comments with argument names and their order.

    An example of a parameters structure:
    - https://github.com/Uniswap/v3-periphery/blob/main/contracts/NonfungiblePositionManager.sol#L128
    - https://github.com/Uniswap/v3-periphery/blob/75f3b72b4412b41e31c2a2370bb52d55f99ec717/contracts/interfaces/INonfungiblePositionManager.sol#L79-L91

- **Finding**: Poor validation of "debt buffer" arguments
    **Severity:** QA
    **Description:**
    These function arguments are not validated according to the documentation:
    - `cushionDebtBuffer` at https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L97
    - `debtBuffer_` at https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L527
    
    In both of these cases, the passed value is checked to be lower than `10000`, however, in [MarketParams
    documentation in IBondAuctioneer](https://github.com/code-423n4/2022-08-olympus/blob/main/src/interfaces/IBondAuctioneer.sol#L31-L35):
    > Minimum is the greater of 10% or initial max payout as a percentage of capacity.
    > The value must be > 10% but can exceed 100% if desired.

    Also, the documentation says:
    > If the value is too small, the market will not be able function normally and close prematurely.

    However, there's no minimal value check in the above functions.