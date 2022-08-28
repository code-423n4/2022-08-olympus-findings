-> X = X + Y IS CHEAPER THAN X += Y (same for X = X - Y IS CHEAPER THAN X -= Y)

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=reserveDebt%5Btoken_%5D%5Bmsg.sender%5D%20%2B%3D%20amount_%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=totalDebt%5Btoken_%5D%20%2B%3D%20amount_%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=reserveDebt%5Btoken_%5D%5Bmsg.sender%5D%20%2D%3D%20received%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=totalDebt%5Btoken_%5D%20%2D%3D%20received%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=oldDebt%20%3C%20amount_)-,totalDebt%5Btoken_%5D%20%2B%3D%20amount_,-%2D%20oldDebt%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=totalDebt%5Btoken_%5D%20%2D%3D%20oldDebt
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#:~:text=_movingAverage%20%2B%3D%20(currentPrice%20%2D%20earliestPrice)%20/%20numObs%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#:~:text=_movingAverage%20%2D%3D%20(earliestPrice%20%2D%20currentPrice)%20/%20numObs%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#:~:text=total%20%2B%3D%20startObservations_%5Bi%5D%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#:~:text=balanceOf%5Bfrom_%5D%20%2D%3D%20amount_%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#:~:text=balanceOf%5Bto_%5D%20%2B%3D%20amount_%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#:~:text=_amountsPerMarket%5Bid_%5D%5B0%5D%20%2B%3D%20inputAmount_%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#:~:text=_amountsPerMarket%5Bid_%5D%5B1%5D%20%2B%3D%20outputAmount_%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#:~:text=lastBeat%20%2B%3D%20frequency()%3B


->STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas)

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#:~:text=uint256%20internal%20immutable%20_scaleFactor%3B

-> ++i costs less gas compared to i++ or i += 1 (Also --i costs less gas compared to i-- or i -= 1)

https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#:~:text=A%2DZ%20only-,unchecked%20%7B,i%2B%2B%3B,-%7D
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#:~:text=%7D-,unchecked%20%7B,i%2B%2B%3B,-%7D
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=/%2010%3B-,decimals%2B%2B,-%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=%3D%20true%3B-,_status.low.count%2B%2B,-%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=%3D%20false%3B-,_status.low.count%2D%2D,-%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=_status.high.count%2B%2B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=_status.high.count%2D%2D


-> USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#:~:text=%3D%3E%20mapping(-,bytes4,-%3D%3E%20bool)))
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#:~:text=)%20pure%20returns-,(bytes5),-%7B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=override%20returns%20(-,uint8%20major%2C,-uint8%20minor)%20%7B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#:~:text=uint8%20major%2C-,uint8%20minor),-%7B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#:~:text=by%20the%20contract.-,uint8,-public%20constant%20decimals
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#:~:text=_ohmEthPriceFeed%20%3D%20ohmEthPriceFeed_%3B-,uint8,-ohmEthDecimals%20%3D%20_ohmEthPriceFeed
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#:~:text=override%20returns%20(-,uint8%20major,-%2C%20uint8%20minor
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#:~:text=uint8%20major%2C-,uint8%20minor),-%7B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=public%20immutable%20ohm%3B-,uint8,-public%20immutable%20ohmDecimals
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=public%20immutable%20reserve%3B-,uint8,-public%20immutable%20reserveDecimals
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=is%20the%20priceDecimal%20value-,int8,-priceDecimals%20%3D%20_getPriceDecimals(range
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=cushion.high.price)%3B-,int8,-scaleAdjustment%20%3D%20int8
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=int8%20scaleAdjustment%20%3D-,int8,-(ohmDecimals)%20%2D
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=(ohmDecimals)%20%2D-,int8,-(reserveDecimals)%20%2B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=uint256%20bondScale%20%3D%2010%20**-,uint8(,-36%20%2B%20scaleAdjustment%20%2B%20int8(reserveDecimals
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=the%20low%20side-,uint8,-oracleDecimals%20%3D%20PRICE
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=is%20the%20priceDecimal%20value-,int8,-priceDecimals%20%3D%20_getPriceDecimals(invCushionPrice
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=_getPriceDecimals(invCushionPrice)%3B-,int8,-scaleAdjustment%20%3D%20int8
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=int8%20scaleAdjustment%20%3D-,int8,-(reserveDecimals)%20%2D
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=(reserveDecimals)%20%2D-,int8,-(ohmDecimals)%20%2B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=%3D%2010**-,uint8,-(int8(oracleDecimals
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=**uint8(-,int8,-(oracleDecimals)%20%2D
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=%2B%20scaleAdjustment%20%2B-,int8,-(ohmDecimals)%20%2D
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=(ohmDecimals)%20%2D-,int8,-(reserveDecimals)%20%2D
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=return%20decimals%20%2D-,int8,-(PRICE.decimals


->IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#:~:text=for%20(-,uint256%20i%20%3D%200%3B,-i%20%3C%205
https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#:~:text=for-,(uint256%20i%20%3D%200,-%3B%20i%20%3C%2032


->USING BOOLS FOR STORAGE INCURS OVERHEAD

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#:~:text=mapping(bytes4%20%3D%3E%20bool)))
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#:~:text=mapping(Role%20%3D%3E%20bool))
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#:~:text=if%20role%20exists.-,mapping(Role%20%3D%3E%20bool),-public%20isRole%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#:~:text=and%20therefore%20active).-,bool,-public%20initialized%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=bool%20public%20initialized%3B
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#:~:text=Operator%20is%20active-,bool%20public%20active%3B,-///%20Modules
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#:~:text=mapping(uint256%20%3D%3E%20bool)
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#:~:text=stopped%2C%20true%20%3D%20beating-,bool%20public%20active%3B,-///%20%40notice%20Timestamp%20of
https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#:~:text=mapping(address%20%3D%3E%20bool)

