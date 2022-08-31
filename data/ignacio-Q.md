# 1  RETURN VALUES OF TRANSFER()/TRANSFERFROM() NOT CHECKED 
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L312

# 2 USE OF BLOCK.TIMESTAMP
 A miner can manipulate the block timestamp which can be used to their advantage to attack a smart contract via Block Timestamp Manipulation

## Vulnerability details
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

Blocks have a timestamp field in the block header which is set by the miner and can be changed to whatever they want (with some restriction). In order for a miner to set a block timestamp they need to win the next block and abide by the following time constrains:

The next timestamp is after the last block timestamp
The timestamp can not be too far into the future
If the miner wins a block they can slightly change the block timestamp to their advantage.

## Impact
Dishonest  Miners can influence the value of block.timestamp to perform Maximal Extractable Value (MEV) attacks.
The use of now creates a risk that time manipulation can be performed to manipulate price oracles. Miners can modify the timestamp by up to 900 seconds , Usually to an extent of few seconds on Ethereum, or generally few percent of the block time on any EVM-compatible PoW network.
# # Lines of code
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L85 
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L92
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L135
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L138
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L148
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L150
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L191
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L200
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L207
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L231
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L233

## Recommended Mitigation Steps
Use block.number instead of  block.timestamp or now to reduce the risk of
MEV attacks
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.
### here some reference :
https://www.bookstack.cn/read/ethereumbook-en/spilt.14.c2a6b48ca6e1e33c.md
https://ethereum.stackexchange.com/questions/108033/what-do-i-need-to-be-careful-about-when-using-block-timestamp

# 3 THE CONTRACT SHOULD SAFEAPPROVE(0) FIRST

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L167 
Some tokens  do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.
When trying to re-approve an already approved token, all transactions revert and the protocol cannot be used.
## Recommended Mitigation Steps

Approve with a zero amount first before setting the actual amount : 

ohm.safeApprove(address(MINTR) , 0);
ohm.safeApprove(address(MINTR), type(uint256).max);
# 4 SAFEAPPROVE() IS DEPRECATED 
Deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L167

# 5  MISSING INITIALIZER MODIFIER ON CONSTRUCTOR
OpenZeppelin recommends that the initializer modifier be applied to constructors in order to avoid potential griefs, social engineering, or exploits. Ensure that the modifier is applied to the implementation contract. If the default constructor is currently being used, it should be changed to be an explicit one with the modifier applied.
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L77
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L38

# 6 ZERO-ADDRESS CHECKS ARE MISSING

Zero-address checks are a best practice for input validation of critical address parameters. While the codebase applies this to most cases, there are many places where this is missing in constructors and setters.

Impact: Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L43
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L73
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L39
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L138




