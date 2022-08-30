# Low risk
## [L-01] Missing check for address(0) when assigning value to address state variable.
Zero address checking is the best practice to prevent burning fee, unexpected exceptions or the redeployment of the contract. 

1 instances:

- https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L15

Consider adding a modifier at the start of this function to check it.

*This is low risk because any risk can only result of an owner's error . This can easily be prevented.*


# Non Critical
## [N-01] Typo
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L295
	- `deactive` -> `disable/deactivate`
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L326
	- `deactive` -> `disable/deactivate`
- https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L126
	-	`numbe` -> `number`
