
1. Add a function to update both of movingAverageDuration and observationFrequency

movingAverageDuration and observationFrequency can be changed by changeMovingAverageDuration and changeObservationFrequency respectively in PriceConfig and PRICE.
If we want to change both, from (mAD1, oF1) to (mAD2, oF2), there can be a problem.
If oF1 is not a multiple of mAD2, and oF2 is not a multiple of mAD1, we need another intermediate path (mAD3, oF3). It can be (gcd(mAD1, mAD2), oF2) or (mAD2, lcm(oF1, oF2)).
Anyway, it is not elegant, and it is the best experience to add a function which changes two parameters at the same time.

2. cushionFactor is not validated in the Operator.constructor. But it is validated in Operator.setCushionFactor. I can find the default value in Deploy.sol, but it is better to validate it in the constructor.

3. Range.thresholdFactor, cushionSpread, wallSpread are not validated in the RANGE.contructor.

4. In Heart policy, _operator cannot be changed. So Heart needs to be upgraded after Operator is changed. But in BondCallback, we can set operator to it. So I think it is better to add a setOperator function to Heart policy.

5. Check fee-on-transfer for reserve token in Operator policy
Fee-on-transfer token is not allowed so the balance is checked before and after in BondCallback.sol.
In Operator L299, same check is needed because actual income can be less than amountIn_.

6. Add length limit to instructions
When execute instructions, there is no limit in the length of instructions to be executed. 
And those actions consumes gases a lot. So I think it is the best practise to add a limit in the length of instructions when a proposal is submitted.

7. ohm.safeApprove doesn't handle max correctly
ohm.safeApprove is used in configureDependencies of BondCallback and Operator.
And in the OlympusERC20 implementation, type(uint256).max is not maintained and decreased by value.
But in TRSRY, max value is treated correctly in line 147.
And downflow is desired with the same reason as line 57 of VOTES.

8. ohm approved value is not canceled.
We can revoke policy approvals using Treasury.revokePolicyApprovals. But we don't revoke for ohm approve to minter.

9. high capacity
In L781 - L788 of Operator.sol, high capacity is multipled by (1 + 2 * Ws) and it is same as the white paper. 
But in fact, spread is the percentage from center line, so high capacity should be multiplied by (1 + Ws) / (1 - Ws) = 1 + 2 * Ws / (1 - Ws). So the capacity used is smaller than the capacity needed.

10. TreasuryCustodian.revokePolicyApprovals does not check if it supports Policy.
TreasuryCustodian.revokePolicyApprovals has TODO comment section, but did not implement it.


