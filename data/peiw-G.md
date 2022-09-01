- `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (`-` too)
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L138
- Using `private` rather than `public` for constants, saves gas
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L121
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L124
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L127
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L130
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L133
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L137
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59
   - https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L65
- `++i` costs less gas than `++i`, especially when it is used in for-loop
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L49
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L64
    - https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L488



