# [L-01] Chainlink `latestRoundData` missing round validation

Chainlink's `latestRoundData` returns values including roundId, updatedAt, and answeredInRound.
https://docs.chain.link/docs/price-feeds-api-reference/#latestrounddata

This function is called twice
1. https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L161
2. https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L170

The code calling this chainlink function checks the updatedAt value, but does not have a check that `answeredInRound >= roundId`. This could cause a stale price to be used from an old round. All Chainlink best practice guides and previous audits suggest this check be added, including https://consensys.net/diligence/audits/2021/09/fei-protocol-v2-phase-1/#chainlinkoraclewrapper---latestrounddata-might-return-stale-results 

## Recommended Mitigation Steps

Add this one line to check the outputs of `latestRoundData`
```
require(answeredInRound >= roundId, "Stale price");
```