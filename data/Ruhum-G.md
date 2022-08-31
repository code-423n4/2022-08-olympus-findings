# Gas Report

- [G-01: use immutable variables](#g-01-use-immutable-variables)
- [G-02: use calldata instead of memory](#g-02-use-calldata-instead-of-memory)
- [G-03: remove unnecessary external calls](#g-03-remove-unnecessary-external-calls)
- [G-04: `Operator.swap()` save gas by burning ohm tokens from the user](#g-04-operatorswap-save-gas-by-burning-ohm-tokens-from-the-user)

## G-01: use immutable variables

There are spots where you could but don't use immutable variables:

- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L28
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L32

## G-02: use calldata instead of memory

```
./src/Kernel.sol:393:        Permissions[] memory requests_,
./src/policies//PriceConfig.sol:45:    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
./src/policies//Heart.sol:69:    function configureDependencies() external override returns (Keycode[] memory dependencies) {
./src/policies//Heart.sol:81:        returns (Permissions[] memory permissions)
./src/policies//TreasuryCustodian.sol:53:    function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {
./src/policies//BondCallback.sol:152:    function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {
./src/modules/PRICE.sol:205:    function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
./src/modules/RANGE.sol:79:        ERC20[2] memory tokens_,
./src/modules/RANGE.sol:80:        uint256[3] memory rangeParams_ // [thresholdFactor, cushionSpread, wallSpread]
```
## G-03: remove unnecessary external calls

You can skip the final balance check here and just set to value to 0. There are no known tokens where this explicit check would be necessary AFAIK.

- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L160

## G-04: `Operator.swap()` save gas by burning ohm tokens from the user

Instead of first transferring the tokens to the Operator contract and then burning them, burn them from the user directly.

- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L299-L302

