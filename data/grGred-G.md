### Gas optimizations

### Use `internal view` functions in modifiers to save bytecode

It is recommended to move the modifiers require statements into an `internal virtual function`. This reduces the size of compiled contracts that use the modifiers. Putting the require in an internal function decreases contract size when modifier is used multiple times. There is no difference in deployment gas cost with `private` and `internal` functions.

With Solidity `0.8.14` and optimisations on (200):

```Solidity
contract Ownable is Context {
    address public owner = _msgSender();

    modifier onlyOwner() {
        require(owner == _msgSender(), "Ownable: caller is not the owner");
        _;
    }
}

contract Ownable2 is Context {
    address public owner = _msgSender();

    modifier onlyOwner() {
        _checkOwner();
        _;
    }

    function _checkOwner() internal view virtual {
        require(owner == _msgSender(), "Ownable: caller is not the owner");
    }
}

// This is deployment gas cost for each function
// 0: 107172
// 1: 145772
// 2: 181610
// 3: 198170
// 4: 214532
// 5: 241059
contract T1 is Ownable {
    event Call(bytes4 selector);
    function f0() external onlyOwner() { emit Call(this.f0.selector); }
    function f1() external onlyOwner() { emit Call(this.f1.selector); }
    function f2() external onlyOwner() { emit Call(this.f2.selector); }
    function f3() external onlyOwner() { emit Call(this.f3.selector); }
    function f4() external onlyOwner() { emit Call(this.f4.selector); }
}

// 0: 107172
// 1: 147908
// 2: 165818
// 3: 183506
// 4: 192500
// 5: 211682
contract T2 is Ownable2 {
    event Call(bytes4 selector);
    function f0() external onlyOwner() { emit Call(this.f0.selector); }
    function f1() external onlyOwner() { emit Call(this.f1.selector); }
    function f2() external onlyOwner() { emit Call(this.f2.selector); }
    function f3() external onlyOwner() { emit Call(this.f3.selector); }
    function f4() external onlyOwner() { emit Call(this.f4.selector); }
}
```

See:
[Optimize Ownable and Pausable modifiers' size impact #3347](https://github.com/OpenZeppelin/openzeppelin-contracts/pull/3347/)
[Reduce contract size and deployment gas for onlyOwner modifier](https://github.com/OpenZeppelin/openzeppelin-contracts/pull/3223)

Lines:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L70

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L88

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L119

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L223

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L229

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L188

---

### No need to make last `if` statement since all other cases are already covered

Actions is enum with 7 different cases.
```Solidity
/// @notice Actions to trigger state changes in the kernel. Passed by the executor
enum Actions {
    InstallModule,
    UpgradeModule,
    ActivatePolicy,
    DeactivatePolicy,
    ChangeExecutor,
    ChangeAdmin,
    MigrateKernel
}
```

In the function `executeAction` there is no need in usage an extra `if` statement since all other 6 cases are already covered in `if`.
```Solidity
    /// @notice Main kernel function. Initiates state changes to kernel depending on Action passed in.
    function executeAction(Actions action_, address target_) external onlyExecutor {
        if (action_ == Actions.InstallModule) {
            ensureContract(target_);
            ensureValidKeycode(Module(target_).KEYCODE());
            _installModule(Module(target_));
        } else if (action_ == Actions.UpgradeModule) {
            ensureContract(target_);
            ensureValidKeycode(Module(target_).KEYCODE());
            _upgradeModule(Module(target_));
        } else if (action_ == Actions.ActivatePolicy) {
            ensureContract(target_);
            _activatePolicy(Policy(target_));
        } else if (action_ == Actions.DeactivatePolicy) {
            ensureContract(target_);
            _deactivatePolicy(Policy(target_));
        } else if (action_ == Actions.ChangeExecutor) {
            executor = target_;
        } else if (action_ == Actions.ChangeAdmin) {
            admin = target_;
        } else if (action_ == Actions.MigrateKernel) { // @audit no need to make last if statement since all other cases are already covered
            ensureContract(target_);
            _migrateKernel(Kernel(target_));
        }
```

Lines:
https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L254

---

### Help the optimizer by saving a `storage` variable's reference
To help the optimizer, declare a `storage` type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.

```Solidity
function borrow(
   Position storage position = positions[_nftIndex];
```
Lines:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L143

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L269

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L281

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L309

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L310

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L440

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L453

---

### For loops improvement:
* **Caching the length in for loops**

Reading array length at each iteration of the loop takes 6 gas (3 for `mload` and 3 to place `memory_offset` ) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.
I suggest storing the array’s length in a variable before the for-loop.

Example of an array arr and the following loop:
```Solidity
for (uint i = 0; i < length; i++) {
    // do something that doesn't change the value of i
}
``` 
In the above case, the solidity compiler will always read the length of the array during each iteration. 
1. If it is a storage array, this is an extra `sload` operation (100 additional extra gas ([EIP-2929](https://eips.ethereum.org/EIPS/eip-2929)) for each iteration except for the first),
2. If it is a `memory` array, this is an extra `mload` operation (3 additional gas for each iteration except for the first),
3. If it is a `calldata` array, this is an extra `calldataload` operation (3 additional gas for each iteration except for the first)
This extra costs can be avoided by caching the array length (in stack):

```Solidity
uint length = arr.length;
for (uint i = 0; i < length; i++) {
    // do something that doesn't change arr.length
}
```
In the above example, the `sload` or `mload` or `calldataload` operation is only called once and subsequently replaced by a cheap `dupN` instruction. Even though `mload`, `calldataload` and `dupN` have the same gas cost, `mload` and `calldataload` needs an additional `dupN` to put the offset in the stack, i.e., an extra 3 gas.

This optimization is especially important if it is a storage array or if it is a lengthy for loop.

* **The increment in for loop post condition can be made unchecked**

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck [cannot be made inline](https://github.com/ethereum/solidity/issues/10695).

Example for loop:

```Solidity
for (uint i = 0; i < length; i++) {
    // do something that doesn't change the value of i
}
```

In this example, the for loop post condition, i.e., `i++` involves checked arithmetic, which is not required. This is because the value of i is always strictly less than `length <= 2**256 - 1`. Therefore, the theoretical maximum value of i to enter the for-loop body `is 2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. You should manually do this by:

```Solidity
for (uint i = 0; i < length; i = unchecked_inc(i)) {
    // do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
    unchecked {
        return i + 1;
    }
}
```
Or just:
```Solidity
for (uint i = 0; i < length;) {
    // do something that doesn't change the value of i
    unchecked { i++; }
}
```

Note that it’s important that the call to `unchecked_inc` is inlined. This is only possible for solidity versions starting from `0.8.2`.

Gas savings: roughly speaking this can save 30-40 gas per loop iteration. For lengthy loops, this can be significant!
(This is only relevant if you are using the default solidity checked arithmetic.)

* **`++i` costs less gas compared to `i++` or `i += 1`**

`++i `costs less gas compared to `i++` or` i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

Example:
`i++` increments `i` and returns the initial value of `i`. Which means:
```Solidity
uint i = 1; 
i++; // == 1 but i == 2 
```
But `++i` returns the actual incremented value:
```Solidity
uint i = 1; 
++i; // == 2 and i == 2 too, so no need for a temporary variable 
```
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

* **No need to explicitly initialize variables with default values**

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.
As an example:
`for (uint256 i = 0; i < numIterations; ++i) {` 
should be replaced with:
`for (uint256 i; i < numIterations; ++i) {`

* **Don't remove initialization of `i` varible in for loops**

I see a lot of projects where developers mistakenly believe that the removal of `i` vatiable outside of the for loop will save gas. In following snippets you can see that this is wrong:

```Solidity
    function loopCheck1(uint256[] memory arr) external returns (uint256[] memory) {
        gas = gasleft(); // 29863 gas
        uint length = arr.length;
        for (uint i; i < length;) {
            unchecked { ++i; }
        }
        return arr;
        gas -= gasleft();
    }
    
    function loopCheck2(uint256[] memory arr) external  returns (uint256[] memory) {
        gas = gasleft();
        uint i;
        uint length = arr.length;
        for (; i < length;) { // 29912 gas
            unchecked { ++i; }
        }
        return arr;
        gas -= gasleft();
    }
```

* **To sum up, the best gas optimized loop will be:**
```Solidity
uint length = arr.length;
for (uint i; i < length;) {
    unchecked { ++i; }
}
```

Lines:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L397

https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L43

https://github.com/code-423n4/2022-08-olympus/blob/main/src/utils/KernelUtils.sol#L58

---

### Change function visibility from `public` to `external`

Since the function isn't called internally you should change visibility to external. This will save you deployment gas cost.

Lines:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439

https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L451

---

### USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

Lines:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L65

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59

---

### USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

Lines:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L56

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L53

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L50

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L47

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L44

---


### `> 0` is cheaper than `!= 0` sometimes
`!= 0` costs less gas compared to `> 0` for unsigned integers in require statements with the optimizer enabled.
But `> 0` is cheaper than `!=`, with the optimizer enabled and outside a require statement. https://twitter.com/gzeon/status/1485428085885640706

Example with optimizer disabled:
```Solidity
    uint256 public gas;

    function check1() external {
        gas = gasleft();
        require(99999999999999 != 0); // gas 22136 --disabled optimizer
        gas -= gasleft();
    }

    function check2() external {
        gas = gasleft();
        require(99999999999999 > 0); // gas 22136 --disabled optimizer
        gas -= gasleft();
    }

    function check3() external {
        gas = gasleft();
        if (99999999999999 != 0){ // 22149 gas --disabled optimizer
            uint256 i = 123;
        }
        gas -= gasleft();
    }

    function check4() external {
        gas = gasleft();
        if (99999999999999 > 0){ // 22152 gas --disabled optimizer
            uint256 i = 123;
        }
        gas -= gasleft();
    }
```

Example with optimizer enabled:

```Solidity
    uint256 public gas;

    function check1() external {
        gas = gasleft();
        require(99999999999999 != 0); // gas 22106 --enabled optimizer
        gas -= gasleft();
    }

    function check2() external {
        gas = gasleft();
        require(99999999999999 > 0); // gas 22117 --enabled optimizer
        gas -= gasleft();
    }

    function check3() external {
        gas = gasleft();
        if (99999999999999 != 0){ // 22106 gas --enabled optimizer
            uint256 i = 123;
        }
        gas -= gasleft();
    }

    function check4() external {
        gas = gasleft();
        if (99999999999999 > 0){ // 22105 gas --enabled optimizer
            uint256 i = 123;
        }
        gas -= gasleft();
    }
```
Gas savings: it will save you about 10 gas.

```Solidity
To sum up on 0.8.15:
    Without optimizer:
        In require:
            `> 0` equals to `!= 0`
        Outside require:
            `> 0` more expensive than `!= 0` 
    With optimizer:
        In require:
            `> 0` more expensive than `!= 0`
        Outside require:
            `> 0` cheaper than `!= 0`
```
See:
https://twitter.com/gzeon/status/1485428085885640706

Lines:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L79   

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L268 

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L221


---

### Use `calldata` instead of `memory` for function parameters
In some cases, having function arguments in `calldata` instead of `memory` is more optimal. When arguments are read-only on external functions, the data location should be `calldata`.

Example:

```Solidity
contract C {
    function add(uint[] memory arr) external returns (uint sum) {
        uint length = arr.length;
        for (uint i = 0; i < arr.length;) {
            sum += arr[i];
            unchecked { ++i; }
        }
    }
}
```
In the above example, the dynamic array arr has the storage location `memory`. When the function gets called externally, the array values are kept in `calldata` and copied to `memory` during ABI decoding (using the opcode `calldataload` and `mstore`). And during the for loop, `arr[i]` accesses the value in `memory` using a `mload`. However, for the above example this is inefficient. Consider the following snippet instead:

```Solidity
contract C {
    function add(uint[] calldata arr) external returns (uint sum) {
        uint length = arr.length;
        for (uint i = 0; i < arr.length;) {
            sum += arr[i];
            unchecked { ++i; }
        }
    }
}
```
In the above snippet, instead of going via `memory`, the value is directly read from `calldata` using `calldataload`. That is, there are no intermediate `memory` operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with copying value from `calldata` to `memory` in a for loop. Each iteration would cost at least 60 gas. In the latter example, this can be completely avoided. This will also reduce the number of instructions and therefore reduces the deploy time cost of the contract.

In short, use `calldata` instead of `memory` if the function argument is only read.

Note that in older Solidity versions, changing some function arguments from `memory` to `calldata` may cause “unimplemented feature error”. This can be avoided by using a newer (`0.8.*`) Solidity compiler.

Lines:

https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L152

https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205

