## `++i` costs less gas compared to `i++` (same for `--i` vs `i--`)
instances include:

```
src/utils/KernelUtils.sol:49             i++;
src/utils/KernelUtils.sol:64             i++;
src/policies/Operator.sol:488            decimals++;
src/policies/Operator.sol:670                _status.low.count++;
src/policies/Operator.sol:686                _status.high.count++;
src/policies/Operator.sol:675                _status.low.count--;
src/policies/Operator.sol:691                _status.high.count--;
```
