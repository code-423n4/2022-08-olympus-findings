

# Lines of code

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


# Vulnerability details

# Vulnerability details
Blocks have a timestamp field in the block header which is set by the miner and can be changed to whatever they want (with some restriction). In order for a miner to set a block timestamp they need to win the next block and abide by the following time constrains:

The next timestamp is after the last block timestamp
The timestamp can not be too far into the future
If the miner wins a block they can slightly change the block timestamp to their advantage.

## Impact
Dishonest  Miners can influence the value of block.timestamp to perform Maximal Extractable Value (MEV) attacks.
The use of now creates a risk that time manipulation can be performed to manipulate price oracles. Miners can modify the timestamp by up to 900 seconds , Usually to an extent of few seconds on Ethereum, or generally few percent of the block time on any EVM-compatible PoW network.

### here some reference :
https://www.bookstack.cn/read/ethereumbook-en/spilt.14.c2a6b48ca6e1e33c.md
https://ethereum.stackexchange.com/questions/108033/what-do-i-need-to-be-careful-about-when-using-block-timestamp
## Recommended Mitigation Steps
Use block.number instead of  block.timestamp or now to reduce the risk of
MEV attacks