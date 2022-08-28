
## none-critical issue foundings
### N01: EVENT IS MISSING INDEXED FIELDS
#### prof
policies/Governance.sol, 86, b'    event ProposalSubmitted(uint256 proposalId);\r'
policies/Governance.sol, 87, b'    event ProposalEndorsed(uint256 proposalId, address voter, uint256 amount);\r'
policies/Governance.sol, 88, b'    event ProposalActivated(uint256 proposalId, uint256 timestamp);\r'
policies/Governance.sol, 89, b'    event WalletVoted(uint256 proposalId, address voter, bool for_, uint256 userVotes);\r'
policies/Governance.sol, 90, b'    event ProposalExecuted(uint256 proposalId);\r'
policies/Heart.sol, 28, b'    event Beat(uint256 timestamp_);\r'
policies/Heart.sol, 29, b'    event RewardIssued(address to_, uint256 rewardAmount_);\r'
policies/Heart.sol, 30, b'    event RewardUpdated(ERC20 token_, uint256 rewardAmount_);\r'
modules/INSTR.sol, 11, b'    event InstructionsStored(uint256 instructionsId);\r'
policies/Operator.sol, 51, b'    event CushionFactorChanged(uint32 cushionFactor_);\r'
policies/Operator.sol, 52, b'    event CushionParamsChanged(uint32 duration_, uint32 debtBuffer_, uint32 depositInterval_);\r'
policies/Operator.sol, 53, b'    event ReserveFactorChanged(uint32 reserveFactor_);\r'
policies/Operator.sol, 54, b'    event RegenParamsChanged(uint32 wait_, uint32 threshold_, uint32 observe_);\r'
modules/PRICE.sol, 26, b'    event NewObservation(uint256 timestamp_, uint256 price_, uint256 movingAverage_);'
modules/PRICE.sol, 27, b'    event MovingAverageDurationChanged(uint48 movingAverageDuration_);'
modules/PRICE.sol, 28, b'    event ObservationFrequencyChanged(uint48 observationFrequency_);'
modules/RANGE.sol, 20, b'    event WallUp(bool high_, uint256 timestamp_, uint256 capacity_);\r'
modules/RANGE.sol, 21, b'    event WallDown(bool high_, uint256 timestamp_, uint256 capacity_);\r'
modules/RANGE.sol, 22, b'    event CushionUp(bool high_, uint256 timestamp_, uint256 capacity_);\r'
modules/RANGE.sol, 23, b'    event CushionDown(bool high_, uint256 timestamp_);\r'
modules/RANGE.sol, 29, b'    event PricesChanged(\r\n        uint256 wallLowPrice_,\r\n        uint256 cushionLowPrice_,\r\n        uint256 cushionHighPrice_,\r\n        uint256 wallHighPrice_\r\n    );\r'
modules/RANGE.sol, 30, b'    event SpreadsChanged(uint256 cushionSpread_, uint256 wallSpread_);\r'
modules/RANGE.sol, 31, b'    event ThresholdFactorChanged(uint256 thresholdFactor_);\r'

### N02: INCONSISTENT VERSION OF ENGLISH BEING USED
Some functions use American English, whereas others use British English. A single project should use only one of the two
#### prof
interfaces/IBondCallback.sol, 2, b'pragma solidity >=0.8.0;\r'
policies/Governance.sol, 2, b'pragma solidity 0.8.15;\r'
policies/Heart.sol, 2, b'pragma solidity 0.8.15;\r'
interfaces/IBondCallback.sol, 2, b'pragma solidity >=0.8.0;\r'
policies/interfaces/IHeart.sol, 2, b'pragma solidity >=0.8.0;\r'
modules/INSTR.sol, 2, b'pragma solidity 0.8.15;\r'
policies/interfaces/IOperator.sol, 2, b'pragma solidity >=0.8.0;\r'
Kernel.sol, 2, b'pragma solidity 0.8.15;\r'
utils/KernelUtils.sol, 2, b'pragma solidity 0.8.15;\r'
modules/MINTR.sol, 2, b'pragma solidity 0.8.15;\r'
policies/Operator.sol, 2, b'pragma solidity 0.8.15;\r'
modules/PRICE.sol, 2, b'pragma solidity 0.8.15;'
policies/PriceConfig.sol, 2, b'pragma solidity 0.8.15;\r'
modules/RANGE.sol, 2, b'pragma solidity 0.8.15;\r'
policies/TreasuryCustodian.sol, 2, b'pragma solidity 0.8.15;\r'
modules/TRSRY.sol, 2, b'pragma solidity 0.8.15;\r'
policies/VoterRegistration.sol, 2, b'pragma solidity 0.8.15;\r'
modules/VOTES.sol, 2, b'pragma solidity 0.8.15;\r'




## Low risk foundings
### L01: RETURN VALUES OF TRANSFER()/TRANSFERFROM() NOT CHECKED
#### problem
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment
#### prof
policies/Governance.sol, 259, b'        VOTES.transferFrom(msg.sender, address(this), userVotes);\r'
policies/Governance.sol, 312, b'        VOTES.transferFrom(address(this), msg.sender, userVotes);\r'
policies/Heart.sol, 112, b'        rewardToken.safeTransfer(to_, reward);\r'
policies/Heart.sol, 151, b'        token_.safeTransfer(msg.sender, token_.balanceOf(address(this)));\r'
policies/Operator.sol, 330, b'            reserve.safeTransferFrom(msg.sender, address(TRSRY), amountIn_);\r'
policies/Operator.sol, 299, b'            ohm.safeTransferFrom(msg.sender, address(this), amountIn_);\r'
modules/TRSRY.sol, 82, b'        token_.safeTransfer(to_, amount_);\r'
modules/TRSRY.sol, 99, b'        token_.safeTransfer(msg.sender, amount_);\r'
modules/TRSRY.sol, 110, b'        token_.safeTransferFrom(msg.sender, address(this), amount_);\r'

### L02: _SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE
#### problem
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function
#### prof
modules/VOTES.sol, 36, b'        _mint(wallet_, amount_);\r'


### L03: address variable should check if it is zero
#### problem
MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES
#### prof
BondCallback.sol, 43-44

### L04: SAFEAPPROVE() Deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance().
#### problem
If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead
#### prof
policies/BondCallback.sol, 57, b'        ohm.safeApprove(address(MINTR), type(uint256).max);\r'
policies/Operator.sol, 167, b'        ohm.safeApprove(address(MINTR), type(uint256).max);\r'



