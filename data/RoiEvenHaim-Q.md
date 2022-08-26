# Qa

### Unsafe ERC20 Operation(s)

### examples
```
2022-08-olympus\src\policies\Governance.sol::259 => VOTES.transferFrom(msg.sender, address(this), userVotes);
2022-08-olympus\src\policies\Governance.sol::312 => VOTES.transferFrom(address(this), msg.sender, userVotes);
```


### Do not use Deprecated Library Functions

### examples
```
2022-08-olympus\src\policies\BondCallback.sol::57 => ohm.safeApprove(address(MINTR), type(uint256).max);
2022-08-olympus\src\policies\Operator.sol::167 => ohm.safeApprove(address(MINTR), type(uint256).max);
```
