## QA: repeating code can be extracted into a function. These two function can be merged, thus allowing more flexible updates of MADuration and ObsFreq, ie, their updated values would not be constrained current MADuration and ObsFreq, as remainder is required to be 0
[`PRICE.sol#L246-L257`](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L246-L257)
[`PRICE.sol#L272-L289`](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol#L272-L289)

## GAS: can revert earlier to save gas
[`INSTR.sol#L224-L228`](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol#L48)

## GAS: endorseProposal may be called after `proposal.submissionTimestamp + ACTIVATION_DEADLINE`, however at this point, the proposal can no longer be activated, so it is better to revert the endorse
[`Governance.sol#L180`](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L180)

## QA: It does not look reducingBy 0 actually do anything in these two lines?
[`Heart.sol#L92`](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L202)

## QA: it is unclear to me the difference between marketCapacity and capacity in Side. 
updateMarket does not use marketCapacity in any way except for emit.
[`RANGE.sol#L215`](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol#L215)

## QA: missing emits in setters. For example:
[`Kernel.sol#L76`](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L76)
[`Kernel.sol#L126`](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L126)
[`BondCallback.sol#L190`](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/BondCallback.sol#L190)
