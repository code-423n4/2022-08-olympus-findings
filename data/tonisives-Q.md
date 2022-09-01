### Bond markets might become unused, because arbitragers can swap risk free and deplete the Range capacity immediately.

- Swap offers slippage free swaps for instant profits. Bonds and Swap share the same capacity. Arbitragers can realise instant profit with the Swap function. They can possibly use flash loans to increase their swap capacity.
Arbitrage scenarios:
    - If the real price is below MA, the arbitrager can buy OHM with real market price (cheaper) and swap for DAI immediately for instant profit.
    - If the real price is higher than the MA, the arbitrager can swap DAI for OHM at the MA price, and immediately sell at higher market price for instant profit
    
Since the swap function is so profitable, and presumably depletes the capacity of the Range quickly, then is the Bond system actually necessary?
    
If not, then bond markets add complexity to the system, which might introduce new issues or attack vectors.


## Using a semi-centralized currency(DAI) as the RBS reserve asset

According to OlympusDAO’s home page, the OHM token is defined as decentralized and censorship-resistant:

> Olympus is building OHM, a community-owned, decentralized and censorship-resistant reserve currency that is asset-backed, deeply liquid and used widely across Web3.
> 

DAI has USDC collateral which can be frozen by the US government. DAI could potentially lose 50% or more of it’s peg. (3.5B$ - 50% of DAI collateral is USDC). RBS uses [single asset](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol/#L85) (DAI) for it’s calculations.

Therefore it is a bit misleading saying OHM is censorship resistant. However, DAI has plans to sell it's USDC reserve for ETH. This would make DAI censorship resistant, but depeg itself from the $ and cause possible side effects for the RBS system.