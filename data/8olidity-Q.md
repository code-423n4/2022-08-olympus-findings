## SAFEAPPROVE() IS DEPRECATED

Deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead


```
    File: src/policies/BondCallback.sol 
    57┆         ohm.safeApprove(address(MINTR), type(uint256).max);
```

```File: src/policies/Operator.sol 
    167┆         ohm.safeApprove(address(MINTR), type(uint256).max);
```


## SAFETRANSFER/SAFETRANSFERFROM CONSISTENTLY INSTEAD OF TRANSFER/TRANSFERFROM
It is good to add a require() statement that checks the return value of token transfers or
        to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the
        given token reverts in case of a failure. Failure to do so will cause silent failures of
        transfers and affect token accounting in contract.


```
File: src/policies/Governance.sol 
259┆         VOTES.transferFrom(msg.sender, address(this), userVotes);
    ⋮┆----------------------------------------
312┆         VOTES.transferFrom(address(this), msg.sender, userVotes);
    ⋮┆----------------------------------------
```


## Data Location Optimization

The linked function arguments are set as memory yet are declared in external functions.

```
File: src/modules/PRICE.sol:
  205      function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
  206:         external



File: src/policies/BondCallback.sol:
  151      /// @param  tokens_ - Array of tokens to send
  152:     function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {


File: src/policies/Governance.sol:

   70      function requestPermissions()
   71:         external
   72          view

  162          string memory proposalURI_
  163:     ) external {


File: src/policies/PriceConfig.sol:
  45      function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
  46:         external
  47          onlyRole("price_admin")

File: src/policies/TreasuryCustodian.sol
    53┆     function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {
    54┆         if (Policy(policy_).isActive()) revert PolicyStillActive();

```


## Open TODO

```


src/policies/Operator.sol:
  656          /// Get latest price
  657:         /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
  658          /// Current price is guaranteed to be up to date, but may be a bad value if not checked?

src/policies/TreasuryCustodian.sol:
  50      // Anyone can call to revoke a deactivated policy's approvals.
  51:     // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
  52:     // TODO must reorg policy storage to be able to check for deactivated policies.
  53      function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {

  55  
  56:         // TODO Make sure `policy_` is an actual policy and not a random address.
  57  


```