## G001 `++I` COSTS LESS GAS COMPARED TO `I++` OR `I += 1` (SAME FOR `--I` VS `I--` OR `I -= 1`)

Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

[https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L488](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L488)

```solidity
decimals++;
```

# ****G002 - Cache Array Length Outside of Loop****

### **Description**

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

[https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L278](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L278)

```solidity
for (uint256 step; step < instructions.length; ) {
```

Background info

[https://github.com/code-423n4/2021-11-badgerzaps-findings/issues/36](https://github.com/code-423n4/2021-11-badgerzaps-findings/issues/36)

