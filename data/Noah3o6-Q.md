-> EVENT IS MISSING INDEXED FIELDS
Each event should use three indexed fields if there are three or more fields.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#:~:text=event%20PermissionsUpdated(,bool%20granted_
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=event%20ApprovedForWithdrawal(address%20indexed%20policy_%2C%20ERC20%20indexed%20token_%2C%20uint256%20amount_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=event%20DebtIncurred(ERC20%20indexed%20token_%2C%20address%20indexed%20policy_%2C%20uint256%20amount_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=event%20DebtRepaid(ERC20%20indexed%20token_%2C%20address%20indexed%20policy_%2C%20uint256%20amount_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=event%20DebtSet(ERC20%20indexed%20token_%2C%20address%20indexed%20policy_%2C%20uint256%20amount_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#:~:text=event%20WallUp(bool%20high_%2C%20uint256%20timestamp_%2C%20uint256%20capacity_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#:~:text=event%20WallDown(bool%20high_%2C%20uint256%20timestamp_%2C%20uint256%20capacity_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#:~:text=event%20CushionUp(bool%20high_%2C%20uint256%20timestamp_%2C%20uint256%20capacity_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#:~:text=event%20CushionDown(bool%20high_%2C%20uint256%20timestamp_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#:~:text=event%20PricesChanged(,uint256%20wallHighPrice_
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#:~:text=event%20NewObservation(uint256%20timestamp_%2C%20uint256%20price_%2C%20uint256%20movingAverage_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=event%20CushionParamsChanged(uint32%20duration_%2C%20uint32%20debtBuffer_%2C%20uint32%20depositInterval_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#:~:text=event%20ProposalEndorsed(uint256%20proposalId%2C%20address%20voter%2C%20uint256%20amount)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#:~:text=event%20WalletVoted(uint256%20proposalId%2C%20address%20voter%2C%20bool%20for_%2C%20uint256%20userVotes)%3B


-> _SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE

_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#:~:text=ohm.mint(to_%2C%20amount_)%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#:~:text=_mint(wallet_%2C%20amount_)%3B
