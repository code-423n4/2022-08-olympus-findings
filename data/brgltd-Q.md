# [L-01] Missing nonReentrant for function not using checks-effects-interactions

The `batchToTresury` function has access control, but it's updating the state after external calls.
Consider adding a `nonReetrancy` modifier.

```
File: src/policies/BondCallback.sol
function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {
    ERC20 token;
    uint256 balance;
    uint256 len = tokens_.length;
    for (uint256 i; i < len; ) {
        token = tokens_[i];
        balance = token.balanceOf(address(this));
        token.safeTransfer(address(TRSRY), balance);
        priorBalances[token] = token.balanceOf(address(this));

        unchecked {
            ++i;
        }
    }
}
```

# [L-02] Missing zero address checks for setters

Consider adding checks against zero address when a function is receiving an input address.

```
File: main/src/modules/TRSRY.sol
64: function setApprovalFor(
122: function setDebt(
```

# [NC-01] Non library files should use fixed compiler verion

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.
Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

There are 3 instances of this issue.

```
File: src/interfaces/IBondCallback.sol
2: pragma solidity >=0.8.0;
```

```
File: src/policies/interfaces/IHeart.sol
2: pragma solidity >=0.8.0;
```

```
File: src/policies/interfaces/IOperator.sol
2: pragma solidity >=0.8.0;
```

# [NC-02] Public functions not called by the contract should be declared external

There are 2 instances of this issue

```
File: src/policies/Governance.sol
151: function getActiveProposal() public view returns (ActivatedProposal memory) {
145: function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {
```

# [NC-03] Missing NATSPEC

Consider adding NATSPEC on all functions to enhance the project documentation.

```
File: src/policies/Governance.sol
61: function configureDependencies() external override returns (Keycode[] memory dependencies) {
70: function requestPermissions()
```

# [NC-04] Lack of event when kernel grants or revoke status

Consider emitting an event when `setActiveStatus` is called to facilitate monitoring of the system.

```
File: main/src/modules/TRSRY.sol
126: function setActiveStatus(bool activate_) external onlyKernel {
```

# [NC-05] TODOs should should be resolved before deployment

There are 4 instances of this issue.

```
File: src/policies/Operator.sol
657: /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
```

```
File: src/policies/TreasuryCustodian.sol
51: // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
52: // TODO must reorg policy storage to be able to check for deactivated policies.
56: // TODO Make sure `policy_` is an actual policy and not a random address.
```
