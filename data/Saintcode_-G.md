## USE CALLDATA INSTEAD OF MEMORY

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memoryindex. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

When arguments are read-only on external functions, the data location should be calldata

7 instances: 
-Kernel.sol line 393
-PRICE.sol line 205
-BondCallback.sol line 152
-Governance.sol line 162
-Operator lines 96, 97
-TreasuryCustodian..sol line 53

## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

Reading array length at each iteration of the loop consumes more gas than necessary.
In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Consider storing the array’s length in a variable before the for-loop, and use this new variable instead

1 instance:
Governance.sol lines 278

## ++i COSTS LESS GAS THAN i++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS (--I/I-- TOO)

This saves 6 gas per instace

2 instances:
KernelUtils.sol lines 49, 64

## IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: 
`
for (uint256 i = 0; i < numIterations; ++i) { 
`
should be replaced with: 
`
for (uint256 i; i < numIterations; ++i) {
`

3 instances:
Kernel.sol line 397
KernelUtils.sol lines 43, 58


## USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT

This saves 6 gas per instance.

1 instance:
Governance.sol line 247

## USING >= 10  COSTS MORE GAS THAN > 9

1 instance
Operator.sol line 486

## SHORTCIRCUITING OPTIMIZATION

__Kernel.sol line 283:__

The order of the functions inside the if statement in line 283:

`
if (address(oldModule) == address(0) || oldModule == newModule_)
`

can be switched to:
`
if (oldModule == newModule_ || address(oldModule) == address(0))
`
for optimal gas usage because oldModule == newModule_ is more probable to be TRUE hence second one won’t be executed and hence save you gas.

**Operator.sol line 739:**

Checking sideActive in the second part of the if clause is not necessary because if that second part is executed sideActive can only be true:
`
 if (
            !sideActive ||
            (sideActive &&
                auctioneer.isLive(market) &&
                RANGE.capacity(high_) < auctioneer.currentCapacity(market))
        )
`
can be switched to:
`
 if (
            !sideActive ||
            ( auctioneer.isLive(market) &&
                RANGE.capacity(high_) < auctioneer.currentCapacity(market))
        )
`

for optimal gas usage because sideActive is already TRUE hence no need to check it and hence save you gas.


## USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

7 instances:
-Governance.sol 121, 124, 127, 130, 133, 137 
-Operator.sol 89



