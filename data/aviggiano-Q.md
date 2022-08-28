[1]

- Important address changes should follow a "propose" pattern instead of directly setting variables in order to avoid human errors.
- Missing validation on address changes.

e.g. `Kernel.executeAction(Actions,address).target_` on `Actions.ChangeExecutor` and `Actions.ChangeAdmin`:

Before
```
        } else if (action_ == Actions.ChangeExecutor) {
            executor = target_;
        } else if (action_ == Actions.ChangeAdmin) {
            admin = target_;
        }
```

After
```
        } else if (action_ == Actions.ChangeExecutor) {
            if(target_ == address(0)) revert Kernel_InvalidAddress();
            proposedExecutor = target_;
        } else if (action_ == Actions.ChangeAdmin) {
            if(target_ == address(0)) revert Kernel_InvalidAddress();
            proposedAdmin = target_;
        }

        // ...

        function acceptProposedExecutor() external {
            if(msg.sender != proposedExecutor) revert Kernel_InvalidExecutor();
            executor = proposedExecutor;
        }
        function acceptProposedAdmin() external {
            if(msg.sender != proposedAdmin) revert Kernel_InvalidAdmin();
            admin = proposedAdmin;
        }
```

[2]
Missing `RewardUpdated` event on constructor from `Heart.sol`

Before
```
    constructor(
        Kernel kernel_,
        IOperator operator_,
        ERC20 rewardToken_,
        uint256 reward_
    ) Policy(kernel_) {
        _operator = operator_;

        active = true;
        lastBeat = block.timestamp;
        rewardToken = rewardToken_;
        reward = reward_;
    }

```

After
```
    constructor(
        Kernel kernel_,
        IOperator operator_,
        ERC20 rewardToken_,
        uint256 reward_
    ) Policy(kernel_) {
        _operator = operator_;

        active = true;
        lastBeat = block.timestamp;
        rewardToken = rewardToken_;
        reward = reward_;
        emit RewardUpdated(rewardToken_; reward_);
    }

```