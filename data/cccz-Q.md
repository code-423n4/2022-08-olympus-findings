## [Low-01] ensureValidKeycode can be bypassed

### Impact

In the executeAction function of the Kernel contract, when the action is InstallModule or UpgradeModule, the ensureValidKeycode function is used to verify that Module(target_).KEYCODE() consists of uppercase letters.
But after passing ensureValidKeycode verification, newModule_.KEYCODE() will be used again as keycode in _installModule and _upgradeModule functions. Users can bypass ensureValidKeycode() verification by controlling the return value of KEYCODE()

### Proof of Concept
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L235-L243
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L265-L293

### Recommended Mitigation Steps

Consider getting the keycode only once with KEYCODE() and use the keycode later instead of KEYCODE()


## [Low-02] setRewardTokenAndAmount() will front run beat()

### Impact
In the OlympusHeart contract, heart_admin can call setRewardTokenAndAmount to set the reward tokens and reward amount. When the setRewardTokenAndAmount function is executed in the same block as the beat function, it may front run the user's beat, causing the user to receive less reward.

### Proof of Concept
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L92-L114
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L140-L147

### Recommended Mitigation Steps


## [Low-03] The constructor of the Operator does not restrict the cushionFactor

### Impact
There is no restriction on configParams[0] in the constructor of Operator. configParams[0] will be assigned to cushionFactor directly afterwards.
In the setCushionFactor function, the cushionFactor_ is limited to be greater than 100 and less than 10000.
```
    function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {
        /// Confirm factor is within allowed values
        if (cushionFactor_ > 10000 || cushionFactor_ < 100) revert Operator_InvalidParams();

        /// Set factor
        _config.cushionFactor = cushionFactor_;

        emit CushionFactorChanged(cushionFactor_);
    }
```
If an incorrect cushionFactor is set in the constructor of the Operator, this will work because there is no restriction. Later the _activate function will make the created market capacity too large or too small, and may even overflow to make the contract not work
```
            uint256 marketCapacity = range.high.capacity.mulDiv(
                config_.cushionFactor,
                FACTOR_SCALE
            );
```
### Proof of Concept
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L92-L134
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L516-L524
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L388-L391

### Recommended Mitigation Steps

In the constructor of the Operator, limit cushionFactor_ to be greater than 100 and less than 10000.

## [Low-04] OlympusPrice: observations have no minimum length limit

### Impact
The observations array of the OlympusPrice contract is used to calculate the moving average price, which is usually used to prevent price manipulation.
When the length of observations is too small (currently the minimum length is 1), price manipulation cannot be prevented.

### Proof of Concept
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L97-L100
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L242-L249
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L268-L281

### Recommended Mitigation Steps

Consider adding a minimum length limit to observations

## [Low-05] If the beat function of the OlympusHeart contract is not called for a long time, the user can control the moving average price by continuously calling beat

### Impact
When the reward is unattractive (gas consumption is greater than reward tokens) and the price is stable, people have no incentive to call OlympusHeart's beat function to update the price. After a period of time, if the attacker launches a price manipulation attack, the attacker can control the moving average price by continuously calling the beat function.
This is because when the beat function is called, lastBeat will not be updated to the current time, but will add a value.

### Proof of Concept
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L92-L109

### Recommended Mitigation Steps

Consider updating lastBeat to the current time in the beat function