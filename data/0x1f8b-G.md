- [Gas](#gas)
    - [**1. Don't use the length of an array for loops condition**](#1-dont-use-the-length-of-an-array-for-loops-condition)
    - [**2. Reduce boolean comparison**](#2-reduce-boolean-comparison)
        - [Total gas saved: **18 * 2 = 36**](#total-gas-saved-18--2--36)
    - [**3. Avoid compound assignment operator in state variables**](#3-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 18 = 234**](#total-gas-saved-13--18--234)
    - [**4. Shift right instead of dividing by 2**](#4-shift-right-instead-of-dividing-by-2)
        - [Total gas saved: **172 * 2 = 344**](#total-gas-saved-172--2--344)
    - [**5. ++i costs less gas compared to i++ or i += 1**](#5-i-costs-less-gas-compared-to-i-or-i--1)
    - [**6. There's no need to set default values for variables**](#6-theres-no-need-to-set-default-values-for-variables)
        - [Total gas saved: **8 * 2 = 16**](#total-gas-saved-8--2--16)
    - [**7. Change bool to uint256 can save gas**](#7-change-bool-to-uint256-can-save-gas)
    - [**8. Optimize KEYCODE**](#8-optimize-keycode)
        - [Optimize MINTR.KEYCODE](#optimize-mintrkeycode)
        - [Optimize INSTR.KEYCODE](#optimize-instrkeycode)
        - [Optimize VOTES.KEYCODE](#optimize-voteskeycode)
        - [Optimize PRICE.KEYCODE](#optimize-pricekeycode)
        - [Optimize TRSRY.KEYCODE](#optimize-trsrykeycode)
        - [Optimize RANGE.KEYCODE](#optimize-rangekeycode)
    - [**9. Optimize  requestPermissions**](#9-optimize--requestpermissions)
        - [Optimize  OlympusPriceConfig.requestPermissions](#optimize--olympuspriceconfigrequestpermissions)
        - [Optimize VoterRegistration.requestPermissions](#optimize-voterregistrationrequestpermissions)
    - [**10. Optimize configureDependencies using immutable**](#10-optimize-configuredependencies-using-immutable)
        - [Optimize OlympusPriceConfig.configureDependencies](#optimize-olympuspriceconfigconfiguredependencies)
        - [Optimize TreasuryCustodian.configureDependencies](#optimize-treasurycustodianconfiguredependencies)
        - [Optimize BondCallback.configureDependencies](#optimize-bondcallbackconfiguredependencies)
        - [Optimize Governance.configureDependencies](#optimize-governanceconfiguredependencies)
        - [Optimize Operator.configureDependencies](#optimize-operatorconfiguredependencies)
    - [**11. Avoid storage use**](#11-avoid-storage-use)
    - [**12. Use inline instead of a method**](#12-use-inline-instead-of-a-method)
    - [**13. Gas saving using immutable**](#13-gas-saving-using-immutable)
    - [**14. Avoid public constants**](#14-avoid-public-constants)
    - [**15. Reduce math operations**](#15-reduce-math-operations)
        - [Optimize Governance.submitProposal](#optimize-governancesubmitproposal)
        - [Optimize Governance.activateProposal](#optimize-governanceactivateproposal)
        - [Optimize Governance.executeProposal](#optimize-governanceexecuteproposal)
        - [Optimize Operator._activate](#optimize-operator_activate)
    - [**16. Use calldata instead of memory**](#16-use-calldata-instead-of-memory)
    - [**17. Optimze instructions order**](#17-optimze-instructions-order)
        - [Optimize Governance.endorseProposal](#optimize-governanceendorseproposal)
        - [Optimize Governance.endorseProposal](#optimize-governanceendorseproposal)
    - [**18. Optimze Governance storage**](#18-optimze-governance-storage)
    - [**19. delete optimization**](#19-delete-optimization)
        - [Total gas saved: **5 * 1 = 5**](#total-gas-saved-5--1--5)
    - [**20. Optimize Operator.bondPurchase**](#20-optimize-operatorbondpurchase)
    - [**21. Optimize Operator refactoring some methods**](#21-optimize-operator-refactoring-some-methods)
    - [**22. Optimize Operator.setRegenParams and Operator._regenerate**](#22-optimize-operatorsetregenparams-and-operator_regenerate)

# Gas

## **1. Don't use the length of an array for loops condition**

It's cheaper to store the length of the array inside a local variable and iterate over it.

**Affected source code:**

- [Governance.sol:278](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L278)

## **2. Reduce boolean comparison**

It's compared a boolean value using `== true` or `== false`, instead of using the boolean value. `NOT` opcode, it's cheaper to use `EQUAL` or `NOTEQUAL` when the value it's false, or just the value without `== true` when it's true, because it will use less opcodes inside the VM.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testEqual(bool a) public view returns (bool) { return a == true; }
}

contract TesterB {
function testNot(bool a) public view returns (bool) { return a; }
}
```

Gas saving executing: **18 per entry for == true**

```
TesterA.testEqual:   21814
TesterB.testNot:     21796   
```

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testEqual(bool a) public view returns (bool) { return a == false; }
}

contract TesterB {
function testNot(bool a) public view returns (bool) { return !a; }
}
```

Gas saving executing: **15 per entry for == false**

```
TesterA.testEqual:   21814 
TesterB.testNot:     21799
```

**Affected source code:**

Use the value instead of `== true`:

- [Governance.sol:223](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L223)
- [Governance.sol:306](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L306)

### Total gas saved: **18 * 2 = 36**

## **3. Avoid compound assignment operator in state variables**

Using compound assignment operator for state variables (like `State += X` or `State -= X` ...) it's more expensive than use operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint private _a;
function testShort() public {
 _a += 1;
}
}

contract TesterB {
uint private _a;
function testLong() public {
 _a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [PRICE.sol:136](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L136)
- [PRICE.sol:138](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L138)
- [PRICE.sol:222](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L222)
- [TRSRY.sol:96](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L96)
- [TRSRY.sol:97](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L97)
- [TRSRY.sol:115](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L115)
- [TRSRY.sol:116](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L116)
- [TRSRY.sol:131](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L131)
- [TRSRY.sol:132](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L132)
- [VOTES.sol:56](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L56)
- [VOTES.sol:58](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L58)
- [BondCallback.sol:143](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L143)
- [BondCallback.sol:144](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L144)
- [Governance.sol:194](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L194)
- [Governance.sol:198](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L198)
- [Governance.sol:252](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L252)
- [Governance.sol:254](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L254)
- [Heart.sol:103](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L103)

### Total gas saved: **13 * 18 = 234**

## **4. Shift right instead of dividing by 2**

Shifting one to the right will calculate a division by two.

he `SHR` opcode only requires 3 gas, compared to the `DIV` opcode's consumption of 5. Additionally, shifting is used to get around Solidity's division operation's division-by-0 prohibition.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
 function testDiv(uint a) public returns (uint) { return a / 2; }
}

contract TesterB {
 function testShift(uint a) public returns (uint) { return a >> 1; }
}
```

Gas saving executing: **172 per entry**

```
TesterA.testDiv:    21965
TesterB.testShift:  21793   
```

**Affected source code:**

- [Operator.sol:372](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L372)
- [Operator.sol:427](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L427)

### Total gas saved: **172 * 2 = 344**

## **5. `++i` costs less gas compared to `i++` or `i += 1`**

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:

```solidity
uint i = 1;
i++; // == 1 but i == 2
```

But `++i` returns the actual incremented value:

```solidity
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable
```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`
I suggest using `++i` instead of `i++` to increment the value of an uint variable. Same thing for `--i` and `i--`

*Keep in mind that this change can only be made when we are not interested in the value returned by the operation, since the result is different, you only have to apply it when you only want to increase a counter.*

**Affected source code:**

- [Operator.sol:488](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L488)
- [Operator.sol:670](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L670)
- [Operator.sol:675](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L675)
- [Operator.sol:686](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L686)
- [Operator.sol:691](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L691)
- [KernelUtils.sol:49](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L49)
- [KernelUtils.sol:64](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L64)

## **6. There's no need to set default values for variables**

If a variable is not set/initialized, the default value is assumed (0, `false`, 0x0 ... depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testInit() public view returns (uint) { uint a = 0; return a; }
}

contract TesterB {
function testNoInit() public view returns (uint) { uint a; return a; }
}
```

Gas saving executing: **8 per entry**

```
TesterA.testInit:   21392
TesterB.testNoInit: 21384   
```

**Affected source code:**

- [KernelUtils.sol:43](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L43)
- [KernelUtils.sol:58](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/utils/KernelUtils.sol#L58)

### Total gas saved: **8 * 2 = 16**
    
## **7. Change `bool` to `uint256` can save gas**

Because each write operation requires an additional `SLOAD` to read the slot's contents, replace the bits occupied by the boolean, and then write back, `booleans` are more expensive than `uint256` or any other type that uses a complete word. This cannot be turned off because it is the compiler's defense against pointer aliasing and contract upgrades.

Reference:

- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L27

Also, this is applicable to integer types different than `uint256` or `int56`.

**Affected source code for `booleans`:**

- [IOperator.sol:34](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/interfaces/IOperator.sol#L34)
- [Heart.sol:33](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L33)
- [Governance.sol:105](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L105)
- [Governance.sol:117](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L117)
- [Operator.sol:63](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L63)
- [Operator.sol:66](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L66)

**Affected source code for `integers`:**

- [PRICE.sol:44](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L44)
- [PRICE.sol:47](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L47)
- [PRICE.sol:50](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L50)
- [PRICE.sol:53](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L53)
- [PRICE.sol:56](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L56)
- [PRICE.sol:62](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L62)

## **8. Optimize `KEYCODE`**

It is possible to optimize the `KEYCODE` method from the `Module` contract as shown below, or send the keycode to the base constructor to use the immutable in the base contract, as shown below.

```javascript
abstract contract Module is KernelAdapter {
    KeyCode private immutable _KEYCODE;
    constructor(Kernel kernel_, string memory keycode) KernelAdapter(kernel_) { 
        _KEYCODE = toKeycode("MINTR");
    }
    function KEYCODE() public pure virtual returns (Keycode) { return _KEYCODE; }
    ...
}
contract OlympusTreasury is Module, ReentrancyGuard { ...
    constructor(Kernel kernel_) Module(kernel_, "PRICE") {}
    ...
}
```

### Optimize `MINTR.KEYCODE`

**Recommended changes:**

```diff
+   KeyCode private immutable _KEYCODE = toKeycode("MINTR");
    function KEYCODE() public pure override returns (Keycode) {
+       return _KEYCODE;
-       return toKeycode("MINTR");
    }
```

**Affected source code:**

- [MINTR.sol:20-22](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/MINTR.sol#L20-L22)

### Optimize `INSTR.KEYCODE`

**Recommended changes:**

```diff
+   KeyCode private immutable _KEYCODE = toKeycode("INSTR");
    function KEYCODE() public pure override returns (Keycode) {
+       return _KEYCODE;
-       return toKeycode("INSTR");
    }
```

**Affected source code:**

- [INSTR.sol:24](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/INSTR.sol#L24)

### Optimize `VOTES.KEYCODE`

**Recommended changes:**

```diff
+   KeyCode private immutable _KEYCODE = toKeycode("VOTES");
    function KEYCODE() public pure override returns (Keycode) {
+       return _KEYCODE;
-       return toKeycode("VOTES");
    }
```

**Affected source code:**

- [VOTES.sol:23](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/VOTES.sol#L23)

### Optimize `PRICE.KEYCODE`

**Recommended changes:**

```diff
+   KeyCode private immutable _KEYCODE = toKeycode("PRICE");
    function KEYCODE() public pure override returns (Keycode) {
+       return _KEYCODE;
-       return toKeycode("PRICE");
    }
```

**Affected source code:**

- [PRICE.sol:109](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L109)

### Optimize `TRSRY.KEYCODE`

**Recommended changes:**

```diff
+   KeyCode private immutable _KEYCODE = toKeycode("TRSRY");
    function KEYCODE() public pure override returns (Keycode) {
+       return _KEYCODE;
-       return toKeycode("TRSRY");
    }
```

**Affected source code:**

- [TRSRY.sol:48](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/TRSRY.sol#L48)

### Optimize `RANGE.KEYCODE`

**Recommended changes:**

```diff
+   KeyCode private immutable _KEYCODE = toKeycode("RANGE");
    function KEYCODE() public pure override returns (Keycode) {
+       return _KEYCODE;
-       return toKeycode("RANGE");
    }
```

**Affected source code:**

- [RANGE.sol:111](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L111)

## **9. Optimize  `requestPermissions`**
### Optimize  `OlympusPriceConfig.requestPermissions`

**Recommended changes:**

```diff
    function requestPermissions()
        external
        view
        override
        returns (Permissions[] memory permissions)
    {
        permissions = new Permissions[](3);
+       Keycode priceCode = PRICE.KEYCODE();
+       permissions[0] = Permissions(priceCode, PRICE.initialize.selector);
+       permissions[1] = Permissions(priceCode, PRICE.changeMovingAverageDuration.selector);
+       permissions[2] = Permissions(priceCode, PRICE.changeObservationFrequency.selector);
-       permissions[0] = Permissions(PRICE.KEYCODE(), PRICE.initialize.selector);
-       permissions[1] = Permissions(PRICE.KEYCODE(), PRICE.changeMovingAverageDuration.selector);
-       permissions[2] = Permissions(PRICE.KEYCODE(), PRICE.changeObservationFrequency.selector);
    }
```

**Affected source code:**

- [PriceConfig.sol:32-34](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/PriceConfig.sol#L32-L34)

### Optimize `VoterRegistration.requestPermissions`

**Recommended changes:**

```diff
    function requestPermissions()
        external
        view
        override
        returns (Permissions[] memory permissions)
    {
        permissions = new Permissions[](2);
+       Keycode votesCode = VOTES.KEYCODE();
+       permissions[0] = Permissions(votesCode, VOTES.mintTo.selector);
+       permissions[1] = Permissions(votesCode, VOTES.burnFrom.selector);
-       permissions[0] = Permissions(VOTES.KEYCODE(), VOTES.mintTo.selector);
-       permissions[1] = Permissions(VOTES.KEYCODE(), VOTES.burnFrom.selector);
    }
```

**Affected source code:**

- [VoterRegistration.sol:27-36](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/VoterRegistration.sol#L27-L36)

## **10. Optimize `configureDependencies` using `immutable`**

### Optimize `OlympusPriceConfig.configureDependencies`

**Recommended changes:**

```diff
+   KeyCode private immutable PRICE_CODE = toKeycode("PRICE");

    function configureDependencies() external override returns (Keycode[] memory dependencies) {
        dependencies = new Keycode[](1);
-       dependencies[0] = toKeycode("PRICE");
+       dependencies[0] = PRICE_CODE;

        PRICE = OlympusPrice(getModuleAddress(dependencies[0]));
    }
```

**Affected source code:**

- [PriceConfig.sol:20](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/PriceConfig.sol#L20)

### Optimize `TreasuryCustodian.configureDependencies`

**Recommended changes:**

```diff
+   KeyCode private immutable PRICE_CODE = toKeycode("TRSRY");

    function configureDependencies() external override returns (Keycode[] memory dependencies) {
        dependencies = new Keycode[](1);
+       dependencies[0] = PRICE_CODE;
-       dependencies[0] = toKeycode("TRSRY");

        TRSRY = OlympusTreasury(getModuleAddress(dependencies[0]));
    }
```

**Affected source code:**

- [TreasuryCustodian.sol:29](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/TreasuryCustodian.sol#L29)

### Optimize `BondCallback.configureDependencies`

```diff
+   KeyCode private immutable dep0 = toKeycode("TRSRY");
+   KeyCode private immutable dep1 = toKeycode("MINTR");

    function configureDependencies() external override returns (Keycode[] memory dependencies) {
        dependencies = new Keycode[](2);
-       dependencies[0] = toKeycode("TRSRY");
-       dependencies[1] = toKeycode("MINTR");
+       dependencies[0] = dep0;
+       dependencies[1] = dep1;

-       TRSRY = OlympusTreasury(getModuleAddress(dependencies[0]));
-       MINTR = OlympusMinter(getModuleAddress(dependencies[1]));
+       TRSRY = OlympusTreasury(getModuleAddress(dep0));
+       MINTR = OlympusMinter(getModuleAddress(dep1));

        // Approve MINTR for burning OHM (called here so that it is re-approved on updates)
        ohm.safeApprove(address(MINTR), type(uint256).max);
    }
```

**Affected source code:**

- [BondCallback.sol:50-51](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L50-L51)

### Optimize `Governance.configureDependencies`

```diff
+   KeyCode private immutable dep0 = toKeycode("INSTR");
+   KeyCode private immutable dep1 = toKeycode("VOTES");

    function configureDependencies() external override returns (Keycode[] memory dependencies) {
        dependencies = new Keycode[](2);
-       dependencies[0] = toKeycode("INSTR");
-       dependencies[1] = toKeycode("VOTES");
+       dependencies[0] = dep0;
+       dependencies[1] = dep1;

-       INSTR = OlympusInstructions(getModuleAddress(dependencies[0]));
-       VOTES = OlympusVotes(getModuleAddress(dependencies[1]));
+       INSTR = OlympusInstructions(getModuleAddress(dep0));
+       VOTES = OlympusVotes(getModuleAddress(dep1));
    }
```

**Affected source code:**

- [Governance.sol:61-68](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L61-L68)

### Optimize `Operator.configureDependencies`

```diff
+   KeyCode private immutable dep0 = toKeycode("PRICE");
+   KeyCode private immutable dep1 = toKeycode("RANGE");
+   KeyCode private immutable dep2 = toKeycode("TRSRY");
+   KeyCode private immutable dep3 = toKeycode("MINTR");

    function configureDependencies() external override returns (Keycode[] memory dependencies) {
        dependencies = new Keycode[](4);
-       dependencies[0] = toKeycode("PRICE");
-       dependencies[1] = toKeycode("RANGE");
-       dependencies[2] = toKeycode("TRSRY");
-       dependencies[3] = toKeycode("MINTR");
+       dependencies[0] = dep0;
+       dependencies[1] = dep1;
+       dependencies[2] = dep2;
+       dependencies[3] = dep3;

-       PRICE = OlympusPrice(getModuleAddress(dependencies[0]));
-       RANGE = OlympusRange(getModuleAddress(dependencies[1]));
-       TRSRY = OlympusTreasury(getModuleAddress(dependencies[2]));
-       MINTR = OlympusMinter(getModuleAddress(dependencies[3]));
+       PRICE = OlympusPrice(getModuleAddress(dep0));
+       RANGE = OlympusRange(getModuleAddress(dep1));
+       TRSRY = OlympusTreasury(getModuleAddress(dep2));
+       MINTR = OlympusMinter(getModuleAddress(dep3));

        /// Approve MINTR for burning OHM (called here so that it is re-approved on updates)
        ohm.safeApprove(address(MINTR), type(uint256).max);
    }
```

**Affected source code:**

- [Operator.sol:154-168](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L154-L168)

## **11. Avoid storage use**

It is possible to avoid storage accesses by taking advantage of memory variables that have the same value, as shown below.

**Recommended changes:**

```diff
    constructor(
        Kernel kernel_,
        AggregatorV2V3Interface ohmEthPriceFeed_,
        AggregatorV2V3Interface reserveEthPriceFeed_,
        uint48 observationFrequency_,
        uint48 movingAverageDuration_
    ) Module(kernel_) {
        /// @dev Moving Average Duration should be divisible by Observation Frequency to get a whole number of observations
        if (movingAverageDuration_ == 0 || movingAverageDuration_ % observationFrequency_ != 0)
            revert Price_InvalidParams();

        // Set price feeds, decimals, and scale factor
        _ohmEthPriceFeed = ohmEthPriceFeed_;
-       uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();
+       uint8 ohmEthDecimals = ohmEthPriceFeed_.decimals();

        _reserveEthPriceFeed = reserveEthPriceFeed_;
-       uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();
+       uint8 reserveEthDecimals = reserveEthPriceFeed_.decimals();
```

**Affected source code:**

- [PRICE.sol:84](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L84)
- [PRICE.sol:87](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L87)

## **12. Use `inline` instead of a method**

The following methods can be moved to inline calls without greatly affecting readability, this will increase the performance of the contract.

**Recommended changes:**

```diff
    function beat() external nonReentrant {
        ...

        // Issue reward to sender
-       _issueReward(msg.sender);
+       rewardToken.safeTransfer(msg.sender, reward);
+       emit RewardIssued(msg.sender, reward);
        emit Beat(block.timestamp);
    }

-   function _issueReward(address to_) internal {
-       rewardToken.safeTransfer(to_, reward);
-       emit RewardIssued(to_, reward);
-   }
```

**Affected source code:**

- [Heart.sol:111-114](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L111-L114)

## **13. Gas saving using `immutable`**

It's possible to avoid storage access a save gas using `immutable` keyword for the following variables:

It's also better to remove the initial values, because they will be set during the constructor.

**Affected source code:**

- [BondCallback.sol:28](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L28)
- [BondCallback.sol:32](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/BondCallback.sol#L32)

## **14. Avoid public constants**

The number of public methods increase the bytecode of the contract, in addition to the possible attack vectors, reducing the number to the minimum necessary for the contract to work normally is a good practice for both security and gas savings.

It can be more efficient to change constants that shouldn't be made `public` to private or `internal` to stay away from pointless getter functions.

**Affected source code:**

- [Operator.sol:89](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L89)
- [PRICE.sol:59](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/PRICE.sol#L59)
- [RANGE.sol:65](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L65)
- [RANGE.sol:65](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/modules/RANGE.sol#L65)
- [Governance.sol:121](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L121)
- [Governance.sol:124](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L124)
- [Governance.sol:127](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L127)
- [Governance.sol:130](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L130)
- [Governance.sol:133](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L133)
- [Governance.sol:137](https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Governance.sol#L137)

## **15. Reduce math operations**
### Optimize `Governance.submitProposal`

It is possible to reduce the condition of the `submitProposal` method in the following way, since it is not necessary to multiply in both places.

```diff
function submitProposal(
        Instruction[] calldata instructions_,
        bytes32 title_,
        string memory proposalURI_
    ) external {
-       if (VOTES.balanceOf(msg.sender) * 10000 < VOTES.totalSupply() * SUBMISSION_REQUIREMENT)
+       if (VOTES.balanceOf(msg.sender) * SUBMISSION_REQUIREMENT < VOTES.totalSupply())
            revert NotEnoughVotesToPropose();
```

**Affected source code:**

- [Governance.sol:164](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L164)

### Optimize `Governance.activateProposal`

```diff
    function activateProposal(uint256 proposalId_) external {
        ...
        if (
-           (totalEndorsementsForProposal[proposalId_] * 100) <
-           VOTES.totalSupply() * ENDORSEMENT_THRESHOLD
+           (totalEndorsementsForProposal[proposalId_] * 50) <
+           VOTES.totalSupply()
        ) {
            revert NotEnoughEndorsementsToActivateProposal();
        }
        ...
    }
```

**Affected source code:**

- [Governance.sol:217-218](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L217-L218)

### Optimize `Governance.executeProposal` 

```diff
-       if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {
+       if (netVotes * 3 < VOTES.totalSupply()) {
            revert NotEnoughVotesToExecute();
        }
```

**Affected source code:**

- [Governance.sol:268](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L268)

### Optimize `Operator._activate`

It's possible to avoid the duplicate operation of `10**(oracleDecimals * 2)` like following:

```diff
-           uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
-           uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
+           uint256 invCushionPrice = 10**(oracleDecimals * 2);
+           uint256 invWallPrice = invCushionPrice / range.wall.low.price;
+           invCushionPrice /= range.cushion.low.price;
```

**Affected source code:**

- [Operator.sol:419-420](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L419-L420)

## **16. Use `calldata` instead of `memory`**

The method `propose` is `external`, and the arguments are defined as `memory` instead of as `calldata`.

By marking the function as `external` it is possible to use `calldata` in the arguments shown below and save significant gas.

**Recommended change:**

```diff
    function submitProposal(
        Instruction[] calldata instructions_,
        bytes32 title_,
-       string memory proposalURI_
+       string calldata proposalURI_
    ) external {
   ...
   }
```

**Affected source code:**

- [Governance.sol:162](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L162)

## **17. Optimze instructions order**

It is recommended to always perform input or parameter checks first, from cheapest to most expensive. Avoiding making external calls or unnecessary costly tasks for the cases to be verified.

### Optimize `Governance.endorseProposal`
**Recommended change:**

```diff
    function endorseProposal(uint256 proposalId_) external {
-       uint256 userVotes = VOTES.balanceOf(msg.sender);

        if (proposalId_ == 0) {
            revert CannotEndorseNullProposal();
        }

        Instruction[] memory instructions = INSTR.getInstructions(proposalId_);
        if (instructions.length == 0) {
            revert CannotEndorseInvalidProposal();
        }

        // undo any previous endorsement the user made on these instructions
        uint256 previousEndorsement = userEndorsementsForProposal[proposalId_][msg.sender];
        totalEndorsementsForProposal[proposalId_] -= previousEndorsement;

        // reapply user endorsements with most up-to-date votes
+       uint256 userVotes = VOTES.balanceOf(msg.sender);
        userEndorsementsForProposal[proposalId_][msg.sender] = userVotes;
        totalEndorsementsForProposal[proposalId_] += userVotes;

        emit ProposalEndorsed(proposalId_, msg.sender, userVotes);
    }
```

**Affected source code:**

- [Governance.sol:181](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L181)

### Optimize `Governance.endorseProposal`
**Recommended change:**

```diff
    function vote(bool for_) external {
-       uint256 userVotes = VOTES.balanceOf(msg.sender);

        if (activeProposal.proposalId == 0) {
            revert NoActiveProposalDetected();
        }

        if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
            revert UserAlreadyVoted();
        }

+       uint256 userVotes = VOTES.balanceOf(msg.sender);
        if (for_) {
            yesVotesForProposal[activeProposal.proposalId] += userVotes;
        } else {
            noVotesForProposal[activeProposal.proposalId] += userVotes;
        }

        userVotesForProposal[activeProposal.proposalId][msg.sender] = userVotes;

        VOTES.transferFrom(msg.sender, address(this), userVotes);

        emit WalletVoted(activeProposal.proposalId, msg.sender, for_, userVotes);
    }
```

**Affected source code:**

- [Governance.sol:241](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L241)

## **18. Optimze `Governance` storage**

It is possible to save all duplicate keys between `yesVotesForProposal` and `noVotesForProposal` if they both share the same key and use a structure as a value.

**Recommended change:**

```diff
+ struct ActivatedProposal {
+     uint256 yes;
+     uint256 no;
+ }

+   /// @notice Return the total of votes for a proposal id used in calculating net votes.
+   mapping(uint256 => YesNo) public votesForProposal;
-   /// @notice Return the total yes votes for a proposal id used in calculating net votes.
-   mapping(uint256 => uint256) public yesVotesForProposal;

-   /// @notice Return the total no votes for a proposal id used in calculating net votes.
-   mapping(uint256 => uint256) public noVotesForProposal;
```

**Affected source code:**

- [Governance.sol:111](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol#L111)

## **19. `delete` optimization**

Use `delete` instead of set to default value (`false` or `0`).

5 gas could be saved per entry in the following affected lines:

**Affected source code:**

- [Kernel.sol:455](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L455)

### Total gas saved: **5 * 1 = 5**

## **20. Optimize `Operator.bondPurchase`**

It's possible to optimize the following method using an `else` instruction: 

```diff
    function bondPurchase(uint256 id_, uint256 amountOut_)
        external
        onlyWhileActive
        onlyRole("operator_reporter")
    {
        if (id_ == RANGE.market(true)) {
            _updateCapacity(true, amountOut_);
            _checkCushion(true);
        }
+       else if (id_ == RANGE.market(false)) {
-       if (id_ == RANGE.market(false)) {
            _updateCapacity(false, amountOut_);
            _checkCushion(false);
        }
    }
```

**Affected source code:**

- [Operator.sol:346-359](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L346-L359)

## **21. Optimize `Operator` refactoring some methods**

It is possible to remove the `bool high_` argument and the required conditionals in the following methods. It's best to create two methods, one for high and one for low to reduce push arguments and conditional checking, because all the logic is different.

**Affected source code:**

- `_activate` in [Operator.sol:363](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L363)
- `_regenerate` in [Operator.sol:699](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L699)

## **22. Optimize `Operator.setRegenParams` and `Operator._regenerate`**

It is possible to avoid repeated accesses to storage as well as possible human errors such as those posted in the issues about the `lastRegen` field if instead of manually resetting each field in the struct, the entire struct is reset. This will save a considerable amount of gas and reduce human error.

```diff
    function setRegenParams(
        uint32 wait_,
        uint32 threshold_,
        uint32 observe_
    ) external onlyRole("operator_policy") {
        /// Confirm regen parameters are within allowed values
        if (wait_ < 1 hours || threshold_ > observe_ || observe_ == 0)
            revert Operator_InvalidParams();

        /// Set regen params
        _config.regenWait = wait_;
        _config.regenThreshold = threshold_;
        _config.regenObserve = observe_;

-       /// Re-initialize regen structs with new values (except for last regen)
+       /// Re-initialize regen structs with new values
+       Regen memory empty = Regen({
+           count: uint32(0),
+           lastRegen: uint48(block.timestamp),
+           nextObservation: uint32(0),
+           observations: new bool[](observe_)
+       });
+       _status.high = empty;
-       _status.high.count = 0;
-       _status.high.nextObservation = 0;
-       _status.high.observations = new bool[](observe_);

+       _status.low = empty;
-       _status.low.count = 0;
-       _status.low.nextObservation = 0;
-       _status.low.observations = new bool[](observe_);

        emit RegenParamsChanged(wait_, threshold_, observe_);
    }
    
    ...

    function _regenerate(bool high_) internal {
        /// Deactivate cushion if active on the side being regenerated
        _deactivate(high_);

+       Regen memory empty = Regen({
+           count: uint32(0),
+           lastRegen: uint48(block.timestamp),
+           nextObservation: uint32(0),
+           observations: new bool[](_config.regenObserve)
+       });

        if (high_) {

            /// Reset the regeneration data for the side
+           _status.high = empty;
-           _status.high.count = uint32(0);
-           _status.high.observations = new bool[](_config.regenObserve);
-           _status.high.nextObservation = uint32(0);
-           _status.high.lastRegen = uint48(block.timestamp);

            /// Calculate capacity
            uint256 capacity = fullCapacity(true);

            /// Regenerate the side with the capacity
            RANGE.regenerate(true, capacity);
        } else {
            /// Reset the regeneration data for the side
+           _status.low = empty;
-           _status.low.count = uint32(0);
-           _status.low.observations = new bool[](_config.regenObserve);
-           _status.low.nextObservation = uint32(0);
-           _status.low.lastRegen = uint48(block.timestamp);

            /// Calculate capacity
            uint256 capacity = fullCapacity(false);

            /// Regenerate the side with the capacity
            RANGE.regenerate(false, capacity);
        }
    }
```

**Affected source code:**

- [Operator.sol:574-580](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L574-L580)
- [Operator.sol:705-720](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol#L705-L720)
