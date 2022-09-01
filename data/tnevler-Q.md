# Low Risk and Non-Critical Issues 
## Low Risk ##

### [L-01]: Missing check ###

**Context:**

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L79

**Description:**

Missing check that observationFrequency_ not equal to zero.

For example, there is a check that observationFrequency_ not equal to zero [here](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L268).
 
Constructor will not indicate Price_InvalidParams() error without that check.

**Recommendation:**

Change to:  
```
if (observationFrequency_ == 0 || movingAverageDuration_ == 0 || movingAverageDuration_ % observationFrequency_ != 0)
```

## Non-Critical Issues ##

### [N-01]: Constants instead of unknown variables ###
**Context:** 

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L43 (5)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol#L58 (32)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L90 (38)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L245 (10000)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L246 (100)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L247 (10000)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L248 (100)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L264 (10000 and 100)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L164 (10000)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L217 (100)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L268 (100)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L106 (10_000)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L111 (10000 and 100)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L518 (10000 and 100)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L535 (10_000)

+ https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L550 (10000 and 100)


**Description:**

Use constant variables to make the code easier to understand and maintain.

**Recommendation:**

Define constants instead of unknown variables.


### [N-02]: Public function can be external ###
**Context:** 

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L451

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L47

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L75

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L20

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L33

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L37

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L110

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L215

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L108

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L22

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L45

+ https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L28
 

**Description:**

Public functions can be declared external if they are not called by the contract.

**Recommendation:**

Declare these functions as external instead of public.
