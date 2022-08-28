- **Finding**: Unnecessary calldata to memory copying
    **Severity:** Gas optimization
    **Description:**
    Copying function arguments to memory is not necessary when the arguments are not expected to be modified in the
    function. It's recommended to use `calldata` storage type instead of `memory` in these cases:
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L152
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45