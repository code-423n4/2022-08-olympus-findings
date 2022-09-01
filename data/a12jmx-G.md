Switching i++ to ++i in for loops will save about 5 gas per iteration

Contract: KernelUtils.sol

	line 49
	
	Per contract deploy: 428 less gas
	
	line 64
	
	Per contract deploy: 483 less gas
	
	Total per contract deploy: 911 less gas
	
Recommendation:

	++i;

This will also provide consistency across all contracts given that all other for loops are coded as such.