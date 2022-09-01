## LOW RISK 
### 1) `lastBeat` might lag

The variable `lastBeat` is used to store the [timestamp of the last beat](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L35), yet it is incremented only by [`frequency()`](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103). While this would work perfectly in the scenario that keeper bots are consistently calling this function with a frequency of `frequency()` for rewards, `lastBeat` may start lagging behind the actual time in the case there are some update made to the `PRICE` contract (updating observation frequency or the moving average duration) and thus `PRICE` contract is back in the `initialized == false` state which would result in [the call to `updateMovingAverage()` failing](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L97) as the function would revert when `PRICE` is not in the initialized state. This scenario would result in any calls made to the `beat` reverting, and if the `PRICE` contract is not initialized immediately (so there is some delay) after the updates, `lastBeat` would lag behind as it would start getting incremented from the old value up to  old value + multiples of `frequency()`, never reaching the actual timestamp of `beat`s. This can be solved with by making a call to `resetBeat` or by making sure the updates and initializations are done in an atomic fashion operationally, and thus we think this creates only a low risk.  



## NON-CRITICAL
### Open TODOs
There are three open TODOs in this function, and according to our understanding, this function `revokePolicyApprovals` does not require a check to see if the address supplied is a deactivated policy, as `TRSRY` should be only called by policies, and thus only policies would require approvals and revoking the approval of something that is not a policy would not do anything. So, these TODOs can be removed.
(https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L56)

### TYPO
There is a typo in this line (https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L126), `numbe` should be `number`.