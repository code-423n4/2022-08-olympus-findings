## \[G-1\] No need to explicitly initialize variables with default values
If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address...).

```solidity
Kernel.sol#L397			for (uint256 i = 0; i < reqLength; ) {
utils/KernelUtils.sol#L43		for (uint256 i = 0; i < 5; ) {
utils/KernelUtils.sol#L58		for (uint256 i = 0; i < 5; ) {
```
## \[G-02\] Declaring variable inside the loop cost more gas
Most likely because the variable gets reallocated

```solidity
src/Kernel.sol:
	417:	uint256 origIndex = getDependentIndex[keycode][policy_];
	
src/utils/KernelUtils.sol
	44:		 bytes1 char = unwrapped[i];
	59:		 bytes1 char = unwrapped[i];
```

## \[G-3\] Variable packing order

```solidity
src/policies/interfaces/IOperator.sol
	31:		uint32 count; // current number of price points that count towards regeneration
				uint48 lastRegen; // timestamp of the last regeneration
				uint32 nextObservation; // index of the next observation in the observations array
```
Should be
```solidity
	uint32
	uint32
	uint48
```