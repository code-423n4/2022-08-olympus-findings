## L-1. NatSpec comments should be increased in Contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in Contracts

There are many missing NatSpecs throughout the project, if we sample 1 of them
Current Code:
[TRSRY.sol#L63-L72](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L63-L72)
```js
 /// @notice Sets approval for specific withdrawer addresses
    function setApprovalFor(
        address withdrawer_,
        ERC20 token_,
        uint256 amount_
    ) external permissioned {
        withdrawApproval[withdrawer_][token_] = amount_;
        emit ApprovedForWithdrawal(withdrawer_, token_, amount_);
    }
```

Recommendation  Code:
```js
 /**
     * @notice Sets approval for specific withdrawer addresses
     * @dev This function updates the amount of the withdrawApproval mapping value based on the given 3 argument values
     * @param withdrawer_ Withdrawer address from state variable
     * @param token_ instance of ERC20
     * @param amount_ User amount
     * @param permissioned Control of admin for access 
     */
    function setApprovalFor(
        address withdrawer_,
        ERC20 token_,
        uint256 amount_
    ) external permissioned {
        withdrawApproval[withdrawer_][token_] = amount_;
        emit ApprovedForWithdrawal(withdrawer_, token_, amount_);
    }
```

# L-2. Add to Blacklist function

**Description:**
Cryptocurrency mixing service, Tornado Cash, has been blacklisted in the OFAC.
A lot of blockchain companies, token projects, NFT Projects have ```blacklisted``` all Ethereum addresses owned by Tornado Cash listed in the US Treasury Department's sanction against the protocol.
(https://home.treasury.gov/policy-issues/financial-sanctions/recent-actions/20220808)
In addition, these platforms even ban accounts that have received ETH on their account with Tornadocash

Some of these Projects;
* USDC (https://www.circle.com/en/usdc)
* Flashbots (https://www.paradigm.xyz/portfolio/flashbots )
* Aave (https://aave.com/)
* Uniswap
* Balancer
* Infura
* Alchemy 
* Opense
* dYdX

Details on the subject;
https://twitter.com/bantg/status/1556712790894706688?s=20&t=HUTDTeLikUr6Dv9JdMF7AA
https://cryptopotato.com/defi-protocol-aave-bans-justin-sun-after-he-randomly-received-0-1-eth-from-tornado-cash/

For this reason, every project in the Ethereum network must have a blacklist function, this is a good method to avoid legal problems in the future, apart from the current need.

Transactions from the project by an account funded by Tonadocash or banned by OFAC can lead to legal problems.Especially American Citizens may want to get addresses to the blacklist legally, this is not an obligation

```The ban on Tornado Cash makes little sense, because in the end, no one can prevent people from using other mixer smart contracts, or forking the existing ones. It neither hinders cybercrime, nor privacy.```

Here is the most beautiful and close to the project example; Manifold

Manifold Contract
https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```js
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```
Recommended Mitigation Steps add to Blacklist function and modifier


## L-3. Missing Event for Critical Parameters Change

**Context:**
[VoterRegistration.sol#L45-L48](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L45-L48)
[VoterRegistration.sol#L53-L56](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L53-L56)
[TreasuryCustodian.sol#L71-L78](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L71-L78)
[TreasuryCustodian.sol#L80-L87](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L80-L87)
[PriceConfig.sol#L69-L74](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L69-L74)
[PriceConfig.sol#L58-L63](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L58-L63)
[Operator.sol#L624-L62](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L624-L627)
[Governance.sol#L295-L313](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L295-L313)

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit


## L-4. Add to indexed parameter for countable Events

**Context:**
[Heart.sol#L111-L113](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L111-L113)

**Recommendation:**
Add to indexed parameter for countable Events



## L-5. There is no need to cast a variable that is already an address, such as address(x)

**Context:**
[Operator.sol#L100](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L100)
[Operator.sol#L590](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L590)

**Description:**
There is no need to cast a variable that is already an address, such as address(auctioneer_), auctioneer also address.

**Recommendation:**
Use directly variable



## L-6. Open TODOS

**Context:**
[Operator.sol#L657](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L657)
[TreasuryCustodian.sol#L51-L56](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L51-L56)

**Recommendation:**
Use temporary TODOs as you work on a feature, but make sure to treat them before merging. Either add a link to a proper issue in your TODO, or remove it from the code.
