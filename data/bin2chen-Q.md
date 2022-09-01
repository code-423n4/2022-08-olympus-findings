1: 
OlympusRange.sol constructor() need check params
```
    constructor(
        Kernel kernel_,
        ERC20[2] memory tokens_,
        uint256[3] memory rangeParams_ // [thresholdFactor, cushionSpread, wallSpread]
    ) Module(kernel_) {

+++    if (rangeParams_[0] > 10000 || rangeParams_[0] < 100) revert RANGE_InvalidParams();

+++    if (
+++         rangeParams_[2] > 10000 ||
+++         rangeParams_[2] < 100 ||
+++         rangeParams_[1] > 10000 ||
+++         rangeParams_[1] < 100 ||
+++         rangeParams_[1] > rangeParams_[2]
+++    ) revert RANGE_InvalidParams();        

```





2:
OlympusHeart.sol constructor() add emit RewardUpdated，like setRewardTokenAndAmount()
```
    constructor(
        Kernel kernel_,
        IOperator operator_,
        ERC20 rewardToken_,
        uint256 reward_
    ) Policy(kernel_) {

        ...

        rewardToken = rewardToken_;
        reward = reward_;
+++     emit RewardUpdated(token_, reward_);
    }
```    


3:
Kernel.sol executeAction() add check target_！= address(0)

```
   function executeAction(Actions action_, address target_) external onlyExecutor {

        ...

        } else if (action_ == Actions.ChangeExecutor) {
+++         require(target!=address(0));
            executor = target_; 
        } else if (action_ == Actions.ChangeAdmin) {
+++         require(target!=address(0));        
            admin = target_; 
        } else if (action_ == Actions.MigrateKernel) {
        ...
```



4: OlympusVotes mintTo() burnFrom()  add check wallet_ != address(0)
```
    function mintTo(address wallet_, uint256 amount_) external permissioned {    
+++     require(wallet_!=address(0));  
        _mint(wallet_, amount_);
    }

    function burnFrom(address wallet_, uint256 amount_) external permissioned {
+++     require(wallet_!=address(0));      
        _burn(wallet_, amount_);
    }
```