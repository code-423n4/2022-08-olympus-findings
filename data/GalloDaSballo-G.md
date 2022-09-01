# Executive Summary

Codebase is gas conscious and basic gas saving advice is followed pretty thoroughly, below are listed a few extra optimizations, sorted by efficacy

## Optimized `updateMovingAverage` - 200+ gas saved per function call

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L133-L145

```solidity

        // Calculate new moving average
        if (currentPrice > earliestPrice) {
            _movingAverage += (currentPrice - earliestPrice) / numObs;
        } else {
            _movingAverage -= (earliestPrice - currentPrice) / numObs;
        }

        // Push new observation into storage and store timestamp taken at
        observations[nextObsIndex] = currentPrice;
        lastObservationTime = uint48(block.timestamp);
        nextObsIndex = (nextObsIndex + 1) % numObs;

```

Can be changed to

```solidity

        // Calculate new moving average

        /// @audit Use unchecked as you already checked for overflow
        unchecked {
          if (currentPrice > earliestPrice) {
              _movingAverage += (currentPrice - earliestPrice) / numObs;
          } else {
              _movingAverage -= (earliestPrice - currentPrice) / numObs;
          }

          // Push new observation into storage and store timestamp taken at
          /// @audit also unchecked addition
          /// @audit Cache the value of `nextObsIndex` to save an SLOAD
          uint32 cachedNextObsIndex = nextObsIndex;
          observations[cachedNextObsIndex] = currentPrice;
          lastObservationTime = uint48(block.timestamp);
          nextObsIndex = (cachedNextObsIndex + 1) % numObs;
        }

```

## Avoidable Second STATICCALL - 100+ gas

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L238-L239

```solidity
            ensureValidKeycode(Module(target_).KEYCODE());

```

Can instead cache keycode = Module(target_).KEYCODE();
and pass it to the next function `_installModule(target_, keycode);

Saving over 100 gas (STATICCALL + cost of processing the string for the return value)

## Cache Storage Var - Save 100 gas

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L165-L166

```solidity
            if (updatedAt < block.timestamp - 3 * uint256(observationFrequency))

```

Cache the value of `observationFrequency` to save 100 gas

## Free Unchecked - 80+ gas

You can wrap the code below in unchecked to gain around 80 gas;

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L44-L45

```solidity
        uint256 instructionsId = ++totalInstructions;

```


# Usual Suspects

## Cache length - 3 gas

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L278-L279

```solidity
        for (uint256 step; step < instructions.length; ) {

```