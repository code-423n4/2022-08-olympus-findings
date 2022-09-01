### Low Risk Findings Overview
|        | Finding                                          |  Instances  |
|:-------|:-------------------------------------------------|:-----------:|
| [L-01] | DOS of `grantApproval()`                         |      1      |
| [L-02] | Critical function should return `bool`           |      5      |
| [L-03] | Missing check if address is a contract           |      1      |
| [L-04] | `safeApprove` has been deprecated deprecated     |      1      |
| [L-05] | Unsafe `transferfrom()`                          |      2      |
| [L-06] | `executor` transfer should be a two-step process |      1      |
| [L-07] | Missing zero address check                       |      1      |
### Non-critical Findings Overview
|        | Finding                                                                               |  Instances  |
|:-------|:--------------------------------------------------------------------------------------|:-----------:|
| [N-01] | Remove TODOs                                                                          |      3      |
| [N-02] | It is recommend to use scientific notation (`1e18`) instead of exponential (`10**18`) |      1      |
| [N-03] | The use of magic numbers is not recommended                                           |     13      |
| [N-04] | Unindexed parameters in events                                                        |      6      |
### QA overview per contract
| Contract                                                                                                                                                |  Total Instances  |  Total Findings  |  Low Findings  |  Low Instances  |  NC Findings  |  NC Instances  |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------:|:----------------:|:--------------:|:---------------:|:-------------:|:--------------:|
| [Operator.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol)                   |        10         |        4         |       1        |        1        |       3       |       9        |
| [TRSRY.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol)                          |         7         |        2         |       1        |        5        |       1       |       2        |
| [Governance.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol)               |         6         |        3         |       1        |        2        |       2       |       4        |
| [RANGE.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol)                          |         3         |        2         |       0        |        0        |       2       |       3        |
| [TreasuryCustodian.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol) |         3         |        2         |       1        |        1        |       1       |       2        |
| [Kernel.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol)                                |         2         |        2         |       2        |        2        |       0       |       0        |
| [PRICE.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol)                          |         2         |        2         |       0        |        0        |       2       |       2        |
| [BondCallback.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol)           |         1         |        1         |       1        |        1        |       0       |       0        |
| [Heart.sol](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol)                         |         1         |        1         |       0        |        0        |       1       |       1        |
## Low Risk Findings
### [L-01] DOS of `grantApproval()`
The fact anyone can revoke approval without delay or time lock provides an easy way to DOS the granting of approval to other addresses.
This issue is already addressed in `TODO` below, but should not be overlooked as sometimes people make mistakes.
*1 instance of this issue has been found:*
###### [L-01] [TreasuryCustodian.sol#L50-L67](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol#L50-L67)
```solidity

    // Anyone can call to revoke a deactivated policy's approvals.
    // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
    // TODO must reorg policy storage to be able to check for deactivated policies.
    function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {
        if (Policy(policy_).isActive()) revert PolicyStillActive();

        // TODO Make sure `policy_` is an actual policy and not a random address.

        uint256 len = tokens_.length;
        for (uint256 j; j < len; ) {
            TRSRY.setApprovalFor(policy_, tokens_[j], 0);
            unchecked {
                ++j;
            }
        }

        emit ApprovalRevoked(policy_, tokens_);
    }
```
### [L-02] Critical function should return `bool`
The caller of such function should be able to check the return value to ensure the call was successful.
*5 instances of this issue have been found:*
###### [L-02] [TRSRY.sol#L137-L152](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L137-L152)
```solidity

    function _checkApproval(
        address withdrawer_,
        ERC20 token_,
        uint256 amount_
    ) internal {
        // Must be approved
        uint256 approval = withdrawApproval[withdrawer_][token_];
        if (approval < amount_) revert TRSRY_NotApproved();

        // Check for infinite approval
        if (approval != type(uint256).max) {
            unchecked {
                withdrawApproval[withdrawer_][token_] = approval - amount_;
            }
        }
    }
```
###### [L-02b] [TRSRY.sol#L122-L135](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L122-L135)
```solidity

    function setDebt(
        ERC20 token_,
        address debtor_,
        uint256 amount_
    ) external permissioned {
        uint256 oldDebt = reserveDebt[token_][debtor_];

        reserveDebt[token_][debtor_] = amount_;

        if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;
        else totalDebt[token_] -= oldDebt - amount_;

        emit DebtSet(token_, debtor_, amount_);
    }
```
###### [L-02c] [TRSRY.sol#L105-L119](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L105-L119)
```solidity

    function repayLoan(ERC20 token_, uint256 amount_) external nonReentrant {
        if (reserveDebt[token_][msg.sender] == 0) revert TRSRY_NoDebtOutstanding();

        // Deposit from caller first (to handle nonstandard token transfers)
        uint256 prevBalance = token_.balanceOf(address(this));
        token_.safeTransferFrom(msg.sender, address(this), amount_);

        uint256 received = token_.balanceOf(address(this)) - prevBalance;

        // Subtract debt from caller
        reserveDebt[token_][msg.sender] -= received;
        totalDebt[token_] -= received;

        emit DebtRepaid(token_, msg.sender, received);
    }
```
###### [L-02d] [TRSRY.sol#L75-L85](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L75-L85)
```solidity

    function withdrawReserves(
        address to_,
        ERC20 token_,
        uint256 amount_
    ) public {
        _checkApproval(msg.sender, token_, amount_);

        token_.safeTransfer(to_, amount_);

        emit Withdrawal(msg.sender, to_, token_, amount_);
    }
```
###### [L-02e] [TRSRY.sol#L92-L102](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L92-L102)
```solidity

    function getLoan(ERC20 token_, uint256 amount_) external permissioned {
        _checkApproval(msg.sender, token_, amount_);

        // Add debt to caller
        reserveDebt[token_][msg.sender] += amount_;
        totalDebt[token_] += amount_;

        token_.safeTransfer(msg.sender, amount_);

        emit DebtIncurred(token_, msg.sender, amount_);
    }
```
### [L-03] Missing check if address is a contract
No check to ensure `auctioneer_` and `callback_` are contract address and not EOAs.
*1 instance of this issue has been found:*
###### [L-03] [Operator.sol#L586-L595](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L586-L595)
```solidity

    function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_)
        external
        onlyRole("operator_admin")
    {
        if (address(auctioneer_) == address(0) || address(callback_) == address(0))
            revert Operator_InvalidParams();
        /// Set contracts
        auctioneer = auctioneer_;
        callback = callback_;
    }
```
### [L-04] `safeApprove` has been deprecated deprecated
Please use `safeIncreaseAllowance` and `safeDecreaseAllowance` instead.
*1 instance of this issue has been found:*
###### [L-04] [BondCallback.sol#L57-L58](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L57-L58)
```solidity

        ohm.safeApprove(address(MINTR), type(uint256).max);

```
### [L-05] Unsafe `transferfrom()`
Check the return value to ensure the transfer was successful.
*2 instances of this issue have been found:*
###### [L-05] [Governance.sol#L259-L260](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L259-L260)
```solidity

        VOTES.transferFrom(msg.sender, address(this), userVotes);

```
###### [L-05b] [Governance.sol#L312-L313](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L312-L313)
```solidity

        VOTES.transferFrom(address(this), msg.sender, userVotes);

```
### [L-06] `executor` transfer should be a two-step process
If ownership is accidentally transferred to an inactive account all functionalities depending on it will be lost.
*1 instance of this issue has been found:*
###### [L-06] [Kernel.sol#L250-L251](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L250-L251)
```solidity

else if (action_ == Actions.ChangeExecutor) {
            executor = target_;
```
### [L-07] Missing zero address check
Could render kernel unusable. 
*1 instance of this issue has been found:*
###### [L-07] [Kernel.sol#L250-L251](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L250-L251)
```solidity

else if (action_ == Actions.ChangeExecutor) {
            executor = target_;
```

## Non-critical Findings
### [N-01] Remove TODOs
They add unnecessary cluttler and harm readbility for auditors.
*3 instances of this issue have been found:*
###### [N-01] [Operator.sol#L657-L658](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L657-L658)
```solidity

        /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?

```
###### [N-01b] [TreasuryCustodian.sol#L56-L57](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol#L56-L57)
```solidity

        // TODO Make sure `policy_` is an actual policy and not a random address.

```
###### [N-01c] [TreasuryCustodian.sol#L51-L52](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol#L51-L52)
```solidity

// TODO Currently allows anyone to revoke any approval EXCEPT activated policies.
// TODO must reorg policy storage to be able to check for deactivated policies.
```
### [N-02] It is recommend to use scientific notation (`1e18`) instead of exponential (`10**18`)
Improves readbility.
*1 instance of this issue has been found:*
###### [N-02] [PRICE.sol#L91-L92](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L91-L92)
```solidity

        _scaleFactor = 10**exponent;

```
### [N-03] The use of magic numbers is not recommended
Consider setting constant numbers as a `constant` variable for better readability and clarity.
*13 instances of this issue have been found:*
###### [N-03] [Operator.sol#L550-L551](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L550-L551)
```solidity

        if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();

```
###### [N-03b] [Operator.sol#L535-L536](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L535-L536)
```solidity

        if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();

```
###### [N-03c] [Operator.sol#L518-L519](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L518-L519)
```solidity

        if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();

```
###### [N-03d] [Governance.sol#L268-L269](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L268-L269)
```solidity

        if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {

```
###### [N-03e] [Governance.sol#L217-L218](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L217-L218)
```solidity

            (totalEndorsementsForProposal[proposalId_] * 100) <

```
###### [N-03f] [Governance.sol#L164-L165](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L164-L165)
```solidity

        if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)

```
###### [N-03g] [Operator.sol#L550-L551](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L550-L551)
```solidity

        if (reserveFactor_ > 10000 || reserveFactor_ < 100) revert Operator_InvalidParams();

```
###### [N-03h] [Operator.sol#L535-L536](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L535-L536)
```solidity

        if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();

```
###### [N-03i] [Operator.sol#L111-L112](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L111-L112)
```solidity

        if (configParams[4] > 10000 || configParams[4] < 100) revert Operator_InvalidParams();

```
###### [N-03j] [Operator.sol#L106-L107](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L106-L107)
```solidity

        if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();

```
###### [N-03k] [PRICE.sol#L90-L91](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L90-L91)
```solidity

        if (exponent > 38) revert Price_InvalidParams();

```
###### [N-03l] [RANGE.sol#L264-L265](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L264-L265)
```solidity

        if (thresholdFactor_ > 10000 || thresholdFactor_ < 100) revert RANGE_InvalidParams();

```
###### [N-03m] [RANGE.sol#L245-L248](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L245-L248)
```solidity

            wallSpread_ > 10000 ||
            wallSpread_ < 100 ||
            cushionSpread_ > 10000 ||
            cushionSpread_ < 100 ||
```
### [N-04] Unindexed parameters in events 
Events should index all existing parameters (up to three) to facilitate access to off-chain tools that are parsing events. Worth noting every indexed event costs extra gas, so is up to the project to assess the trade-off.
*6 instances of this issue have been found:*
###### [N-04] [Governance.sol#L86-L90](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L86-L90)
```solidity

    event ProposalSubmitted(uint256 proposalId);
    event ProposalEndorsed(uint256 proposalId, address voter, uint256 amount);
    event ProposalActivated(uint256 proposalId, uint256 timestamp);
    event WalletVoted(uint256 proposalId, address voter, bool for_, uint256 userVotes);
    event ProposalExecuted(uint256 proposalId);
```
###### [N-04b] [Operator.sol#L45-L54](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L45-L54)
```solidity

event Swap(
        ERC20 indexed tokenIn_,
        ERC20 indexed tokenOut_,
        uint256 amountIn_,
        uint256 amountOut_
    );
    event CushionFactorChanged(uint32 cushionFactor_);
    event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);
    event ReserveFactorChanged(uint32 reserveFactor_);
    event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);
```
###### [N-04c] [Heart.sol#L28-L30](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L28-L30)
```solidity

    event Beat(uint256 timestamp_);
    event RewardIssued(address to_, uint256 rewardAmount_);
    event RewardUpdated(ERC20 token_, uint256 rewardAmount_);
```
###### [N-04d] [RANGE.sol#L20-L31](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L20-L31)
```solidity

    event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);
    event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);
    event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);
    event CushionDown(bool high_, uint256 timestamp_);
    event PricesChanged(
        uint256 wallLowPrice_,
        uint256 cushionLowPrice_,
        uint256 cushionHighPrice_,
        uint256 wallHighPrice_
    );
    event SpreadsChanged(uint256 cushionSpread_, uint256 wallSpread_);
    event ThresholdFactorChanged(uint256 thresholdFactor_);
```
###### [N-04e] [TRSRY.sol#L27-L29](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L27-L29)
```solidity

    event DebtIncurred(ERC20 indexed token_, address indexed policy_, uint256 amount_);
    event DebtRepaid(ERC20 indexed token_, address indexed policy_, uint256 amount_);
    event DebtSet(ERC20 indexed token_, address indexed policy_, uint256 amount_);
```
###### [N-04f] [TRSRY.sol#L20-L21](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L20-L21)
```solidity

    event ApprovedForWithdrawal(address indexed policy_, ERC20 indexed token_, uint256 amount_);

```