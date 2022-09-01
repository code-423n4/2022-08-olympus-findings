1.

Initializing the variable in the following for loops is unnecessary as they get set to 0 by default

Contract: Kernel.sol

	line 397
	
Contract: KernelUtils.sol

	line 43
        line 58
	
Recommendation:

	for (uint256 i; i < reqLength; )
	
	for (uint256 i; i < 5; ) {
	for (uint256 i; i < 32; ) {

	
This will also provide consistency across all contracts given that all other for loops are coded as such.