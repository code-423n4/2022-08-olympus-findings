### Non critical


### MISSING EVENT FOR CRITICAL PARAMETER CHANGE
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

Lines:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L276

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L292

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L314

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L345

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L371

---

### Update to latest Solidity version (`0.8.16`)
This release fixes one important bug and contains further minor bug fixes and features.

Important Bugfixes:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

For details, please see the [release announcement](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/).
