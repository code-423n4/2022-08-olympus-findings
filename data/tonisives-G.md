```json
contract OlympusRange is Module {
--   uint256 public thresholdFactor;
++   uint16 public thresholdFactor;
```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol/#L63](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol/#L63)

This value never goes over 100%, the maximum amount of a Range's capacity. Thus can be uint16, which covers percentage max value of 10_000.