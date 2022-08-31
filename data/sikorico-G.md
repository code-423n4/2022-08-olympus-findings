
# GAS REPORT

## [GAS 00] transferFrom(address(this), to, amount) can be changed to transfer(to, amount) to save gas


### Proof of concept:
- [Governance.sol#L311](https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L311)
