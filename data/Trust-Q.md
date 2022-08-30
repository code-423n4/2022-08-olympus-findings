Operator's setRegenParams does some validation on the input:
```
        if (wait_ < 1 hours || threshold_ > observe_ || observe_ == 0)
            revert Operator_InvalidParams();
```
However, it must also check that threshold_ != 0, otherwise the system will keep regenerating

Operator's setThresholdFactor updates the current threshold used for determining if walls are depleted. However, this threshold will not take effect until after regenerate() is called, which may be in a long time, and before the new threshold is needed.
Make sure to update the existing walls, if they are active:
```
if (_range.high.active == true) {
    uint256 threshold = (fullCapacity(true) * thresholdFactor) / FACTOR_SCALE;
    _range.high.threshold = threshold
}
if (_range.low.active == true) {
    uint256 threshold = (fullCapacity(false) * thresholdFactor) / FACTOR_SCALE;
    _range.low.threshold = threshold
}
```
