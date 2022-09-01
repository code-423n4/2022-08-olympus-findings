# Summary

## Low-Risk Issues

|       | Issue                                                         | Instances |
| :---: | :------------------------------------------------------------ | :-------: |
| **1** | Consider making contracts Pausable or add emergency functions |     -     |
| **2** | Misleading comment                                            |     1     |
| **3** | Two Step Admin Changes                                        |     2     |

## Non-critical Issues

|       | Issue                                                         | Instances |
| :---: | :------------------------------------------------------------ | :-------: |
| **1** | Misleading name                                               |     1     |
| **2** | Usage of magic number                                         |    13     |
| **3** | Replacing the assembly extcodesize checks for versions >0.8.1 |     1     |
| **4** | Open TODOs                                                    |     2     |
| **5** | Unnecessary event fields                                      |     5     |

## Total:  25 instances over  8 issues

---

**Low-Risk Issues**:

1. **Consider making contracts Pausable or add emergency functions**

    There is not any procedure in case of emergency. This issue requires external circumstances, so I think it is of low severity.
    There are possibilities that the admin, executor, or one of the multi-sig signers' accounts is compromised.
    In this case, as far as I understand Olympus must create a proposal, to change admin/executor, which will take at least 3 days, which is enough for to account stealer to realize that he has access to the contract.

    My suggestion is either to make contracts pausable or add functions that allow admin/executor to pass their role with some timelock, which will serve as fraud prevention for users.

2. **Misleading comment (1 instance)**

    The comment indicates that only 'a'-'z' characters are available, but in fact, '_' and ' ' are also valid.

    - src/utils/KernelUtils.sol:[60-61](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L60-L61)

    ```solidity
    60        if ((char < 0x61 || char > 0x7A) && char != 0x5f && char != 0x00) {
    61            revert InvalidRole(role_); // a-z only
    ```

3. **Two Step Admin Changes (2 instances)**

    I understand that this action can only be executed through a successful proposal with 33% net votes, but relying only on the sanity of the people is unwise. I mean, it's theoretically possible that the proposer made a proposal in human-readable form with the correct address, but a typo was made, or the wrong address is intentionally indicated in the target of the on-chain instruction.

    - src/Kernel.sol:[250-253](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L250-L253)

    ```solidity
    250        } else if (action_ == Actions.ChangeExecutor) {
    251            executor = target_;
    252        } else if (action_ == Actions.ChangeAdmin) {
    253            admin = target_;
    ```

    fix:

    ```solidity
    } else if (action_ == Actions.ChangeExecutor) {
        pending_executor = target_;
    } else if (action_ == Actions.ChangeAdmin) {
        pending_admin = target_;
    ...
    function acceptAdmin/acceptExecutor() external {
        if (msg.sender != pending_admin/pending_executor) revert SomeError();
        admin = pending_admin;/executor = pending_executor;
    }
    ```

---

**Non-Crit issues:**

1. **Misleading name (1 instance)**

    The function is named _updateCapacity, but actually, it reduces capacity by the number of parameters which can lead to misunderstanding. I chose non-severity because the function is internal and only protocol devs are affected

    - src/policies/Operator.sol:[631-640](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L631-L640)

    ```solidity
    631         /// @notice          Update the capacity on the RANGE module.
    632         /// @param high_     Whether to update the high side or low side capacity (true = high, false = low).
    633         /// @param reduceBy_ The amount to reduce the capacity by (OHM tokens for high side, Reserve tokens for low side).
    634         function _updateCapacity(bool high_, uint256 reduceBy_) internal {
    635             /// Initialize update variables, decrement capacity if a reduceBy amount is provided
    636             uint256 capacity = RANGE.capacity(high_) - reduceBy_;
    637
    638             /// Update capacities on the range module for the wall and market
    639             RANGE.updateCapacity(high_, capacity);
    640         }
    ```

2. **Usage of magic number (13 instances)**

    Consider using named constant instead.

   - src/modules/RANGE.sol:[245-248](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L245-L248), [264](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L264)

   ```solidity
   245            wallSpread_ > 10000 ||
   246            wallSpread_ < 100 ||
   247            cushionSpread_ > 10000 ||
   248            cushionSpread_ < 100 ||
   ...
   264        if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();
   ```

   - src/policies/Operator.sol:[103](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L103), [106](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L106), [108](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L108), [111](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L111), [114](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L114)

   ```solidity
   103        if (configParams[1] > uint256(7 days) || configParams[1] < uint256(1 days))
   ...
   106        if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();
   ...
   108        if (configParams[3] < uint32(1 hours) || configParams[3] > configParams[1])
   ...
   111        if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();
   ...
   114            configParams[5] < 1 hours ||
   ```

3. **Replacing the assembly extcodesize checks for versions >0.8.1**

    On the contract check, Inline assembly can be replaced with address(thing).code.length

    - src/utils/KernelUtils.sol:[32-36](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L32-L36)

    ```solidity
    32    uint256 size;
    33    assembly {
    34        size := extcodesize(target_)
    35    }
    36    if (size == 0) revert TargetNotAContract(target_);
    ```

    fix:

    ```solidity
    if (target_.code.length == 0) revert TargetNotAContract(target_);
    ```

4. **Open TODOs (5 instances)**

   - src/policies/TreasuryCustodian.sol:[51-52](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L51-L52), [56](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/TreasuryCustodian.sol#L56)

   ```solidity
   51   // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
   52   // TODO must reorg policy storage to be able to check for deactivated policies.
   ...
   56   // TODO Make sure `policy_` is an actual policy and not a random address.
   ```

   - src/policies/Operator.sol:[657-658](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L657-L658)

   ```solidity
   657  /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?
   658  /// Current price is guaranteed to be up to date, but may be a bad value if not checked?
   ```

5. **Unnecessary event fields (5 instances)**

    - src/modules/RANGE.sol:[20-23](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L20-L23)

    ```solidity
    20     event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);
    21     event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);
    22     event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);
    23     event CushionDown(bool high_, uint256 timestamp_);
    ```

    - src/policies/Heart.sol:[28](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol#L28)

    ```solidity
    28     event Beat(uint256 timestamp_);
    ```
