
## Missing fee parameter validation


Some fee parameters of functions are not checked for invalid values. Validate the parameters:
        
        
### Code instances:

        PRICE.constructor (ohmEthPriceFeed_)
        PRICE.constructor (reserveEthPriceFeed_)



## safeApprove of openZeppelin is deprecated


You use safeApprove of openZeppelin although it's deprecated.
(see https://github.com/OpenZeppelin/openzeppelin-contracts/blob/566a774222707e424896c0c390a84dc3c13bdcb2/contracts/token/ERC20/utils/SafeERC20.sol#L38)
You should change it to increase/decrease Allowance as OpenZeppilin says.
        
### Code instances:

        Deprecated safeApprove in Operator.sol line 166: ohm.safeApprove(address(MINTR), type(uint256).max);
        Deprecated safeApprove in TransferHelper.sol line 40: abi.encodeWithSelector(ERC20.approve.selector, to, amount)
        Deprecated safeApprove in BondCallback.sol line 56: ohm.safeApprove(address(MINTR), type(uint256).max);



## Require with empty message

The following requires are with empty messages. 
This is very important to add a message for any require. So the user has enough information to know the reason of failure.
### Code instance:

        Solidity file: FullMath.sol, In line 44 with Empty Require message.



## Not verified input


external / public functions parameters should be validated to make sure the address is not 0.
Otherwise if not given the right input it can mistakenly lead to loss of user funds.    

### Code instances:

        MINTR.sol.mintOhm to_
        Kernel.sol.revokeRole addr_
        OlympusERC20.sol.burnFrom account_
        OlympusERC20.sol.mint account_
        VOTES.sol.mintTo wallet_
        VOTES.sol.transferFrom to_
        TreasuryCustodian.sol.revokePolicyApprovals policy_
        TreasuryCustodian.sol.increaseDebt debtor_
        VoterRegistration.sol.issueVotesTo wallet_
        TRSRY.sol.setDebt debtor_
        TRSRY.sol.setApprovalFor withdrawer_
        Kernel.sol.grantRole addr_
        VOTES.sol.transferFrom from_
        VOTES.sol.burnFrom wallet_
        Kernel.sol.executeAction target_
        MINTR.sol.burnOhm from_
        VoterRegistration.sol.revokeVotesFrom wallet_
        TreasuryCustodian.sol.grantApproval for_
        TRSRY.sol.withdrawReserves to_
        BondCallback.sol.whitelist teller_
        TreasuryCustodian.sol.decreaseDebt debtor_



## Solidity compiler versions mismatch


The project is compiled with different versions of solidity, which is not recommended because it can lead to undefined behaviors.
        
### Code instance:

        



## Hardcoded WETH

WETH address is hardcoded but it may differ on other chains, e.g. Polygon, 
so make sure to check this before deploying and update if necessary
You should consider injecting WETH address via the constructor.
(previous issue: https://github.com/code-423n4/2021-10-ambire-findings/issues/54)
### Code instance:

        Hardcoded weth address in Deploy.sol



## Named return issue

Users can mistakenly think that the return value is the named return, but it is actually the actualreturn statement that comes after. To know that the user needs to read the code and is confusing.
Furthermore, removing either the actual return or the named return will save gas. 

### Code instances:

        TRSRY.sol, VERSION
        INSTR.sol, VERSION
        MINTR.sol, VERSION
        BondCallback.sol, amountsForMarket
        FullMath.sol, mulDiv
        PRICE.sol, VERSION
        VOTES.sol, VERSION
        RANGE.sol, VERSION



## Missing non reentrancy modifier

The following functions are missing reentrancy modifier although some other pulbic/external functions does use reentrancy modifer.
Even though I did not find a way to exploit it, it seems like those functions should have the nonReentrant modifier as the other functions have it as well..

### Code instances:

        BondCallback.sol, batchToTreasury is missing a reentrancy modifier
        TRSRY.sol, withdrawReserves is missing a reentrancy modifier
        TRSRY.sol, getLoan is missing a reentrancy modifier
        Operator.sol, configureDependencies is missing a reentrancy modifier
        Operator.sol, operate is missing a reentrancy modifier



## Assert instead require to validate user inputs


        From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
        With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.
        
### Code instance:

        OlympusERC20.sol : reachable assert in line 612



## In the following public update functions no value is returned

In the following functions no value is returned, due to which by default value of return will be 0. 
We assumed that after the update you return the latest new value. 
(similar issue here: https://github.com/code-423n4/2021-10-badgerdao-findings/issues/85). 

### Code instances:

        RANGE.sol, updateCapacity
        RANGE.sol, updateMarket
        PRICE.sol, updateMovingAverage
        RANGE.sol, updatePrices



## Never used parameters

Those are functions and parameters pairs that the function doesn't use the parameter. In case those functions are external/public this is even worst since the user is required to put value that never used and can misslead him and waste its time. 

### Code instances:

        VOTES.sol: function transfer parameter amount_ isn't used. (transfer is public)
        VOTES.sol: function transfer parameter to_ isn't used. (transfer is public)



## Open TODOs

Open TODOs can hint at programming or architectural errors that still need to be fixed. 
These files has open TODOs:

### Code instances:

Open TODO in TreasuryCustodian.sol line 55 :         // TODO Make sure `policy_` is an actual policy and not a random address.

Open TODO in Deploy.sol line 144 :             ] // TODO verify initial parameters

Open TODO in Operator.sol line 656 :         /// TODO determine if this should use the last price from the MA or recalculate the current price, ideally last price is ok since it should have been just updated and should include check against secondary?

Open TODO in TreasuryCustodian.sol line 50 :     // TODO Currently allows anyone to revoke any approval EXCEPT activated policies.

Open TODO in OlympusERC20.sol line 663 :     // TODO comment actual hash value.

Open TODO in TreasuryCustodian.sol line 51 :     // TODO must reorg policy storage to be able to check for deactivated policies.




## Check transfer receiver is not 0 to avoid burned money


Transferring tokens to the zero address is usually prohibited to accidentally avoid "burning" tokens by sending them to an unrecoverable zero address.


### Code instances:

        https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L112
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L113
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L298
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L259
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L99
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Heart.sol#L151
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/VOTES.sol#L61
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L159
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Governance.sol#L312
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L82
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L108



## Missing commenting


        The following functions are missing commenting as describe below:
        
### Code instance:

        PriceConfig.sol, changeObservationFrequency (external), parameter observationFrequency_ not commented



## Add a timelock

To give more trust to users: functions that set key/critical variables should be put behind a timelock.

### Code instances:

        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/TRSRY.sol#L64
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L559
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/RANGE.sol#L242
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/Operator.sol#L527



## Must approve 0 first


Some tokens (like USDT) do not work when changing the allowance from an existing non-zero allowance value.
They must first be approved by zero and then the actual allowance must be approved.

### Code instances:

approve without approving 0 first Operator.sol, 166,         ohm.safeApprove(address(MINTR), type(uint256).max);

approve without approving 0 first BondCallback.sol, 56,         ohm.safeApprove(address(MINTR), type(uint256).max);




## Unsafe Cast

use openzeppilin's safeCast in:
 
### Code instances:

        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L266 : unsafe cast uint32(newObservations)
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L240 : unsafe cast uint32(newObservations)



## Unbounded loop on array that can only grow can lead to DoS

A malicious attacker that is also a protocol owner can push unlimitedly to an array, that some function loop over this array.
If increasing the array size enough, calling the function that does a loop over the array will always revert since there is a gas limit.
This is a Med Risk issue since it can lead to DoS with a reasonable chance of having untrusted owner or even an owner that did a mistake in good faith.

### Code instances:

        Kernel.sol (L360): Unbounded loop on the array activePolicies that can be publicly pushed by ['_activatePolicy'] and can't be pulled
        Kernel.sol (L351): Unbounded loop on the array allKeycodes that can be publicly pushed by ['_installModule'] and can't be pulled



## Div by 0


Division by 0 can lead to accidentally revert,
(An example of a similar issue - https://github.com/code-423n4/2021-10-defiprotocol-findings/issues/84)

### Code instances:

        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L177 reserveEthPrice might be 0
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L136 numObs might be 0
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L230 numObs might be 0
        https://github.com/code-423n4/2022-08-olympus/tree/main/src/modules/PRICE.sol#L138 numObs might be 0



## Tokens with fee on transfer are not supported


There are ERC20 tokens that charge fee for every transfer() / transferFrom().

Vault.sol#addValue() assumes that the received amount is the same as the transfer amount, 
and uses it to calculate attributions, balance amounts, etc. 
But, the actual transferred amount can be lower for those tokens.
Therefore it's recommended to use the balance change before and after the transfer instead of the amount.
This way you also support the tokens with transfer fee - that are popular.


### Code instance:

        https://github.com/code-423n4/2022-08-olympus/tree/main/src/policies/BondCallback.sol#L113

