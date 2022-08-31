# GAS REPORT

## [GAS] Caching msg.sender is unnecessary


### Proof of concept:
- [Governance.t.sol#L56](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/policies/Governance.t.sol#L56)
- [RANGE.t.sol#L308](https://github.com/code-423n4/2022-08-olympus/tree/main/src/test/modules/RANGE.t.sol#L308)

## [GAS] Cache the following arrays size before the loop


Example: [Governance.sol#L277](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L277)