# Index
[G01] Post-increment/decrement cost more gas then pre-increment/decrement
[G02] Array length should not be looked up in every loop of a for-loop
[G03] Operatos <= or >= cost more gas than operators < or >
[G04] != 0 is cheaper than > 0
[G05] Variable1 = Variable1 + (-) Variable2 is cheaper in gas cost than variable1 += (-=) variable2.
[G06] Using private rather than public for constants
[G07] Don't compare boolean expressions to boolean literals
[G08] Usage of uints/ints smaller than 32 Bytes (256 bits) incurs overhead
[G09] Initialize variables with default values are not needed
[G10] Using bools for storage incurs overhead
[G11] Multiplication/division by two should use bit shifting
[G12] Calldata vs Memory
[G13] Use a more recent version of solidity
[G14] Using storage instead of memory for structs/arrays
[G15] Tight variable packing 
[G16] Move variable declaration before is going to be used
[G17] Refactoring code
[G18] Use unchecked when it's not possible to overflow
[G19] Internal functions only called once can be inlined to save gas
[G20] Remove unused functions

# Details
## [G01] Post-increment/decrement cost more gas then pre-increment/decrement
### Description
++I (--I) cost less gas than I++ (I--) especially in a loop.

### Proof of concept

```solidity
contract TestPost {
	function testPost() public {
		uint256 i;
		i++;
	}
}
contract TestPre {
	function testPre() public {
		uint256 i;
		++i;
	}
}
```

- Transaction cost of testPost is 21333 gas
- Transaction cost of testPre is 21328 gas 
- After the test it's possible to save **5 gas per ocurrence** with this optimization.

### Lines in the code
[KernelUtils.sol#L49](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/utils/KernelUtils.sol#L49)
[KernelUtils.sol#L64](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/utils/KernelUtils.sol#L64)
[Operator.sol#L488](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L488)
[Operator.sol#L670](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L670)
[Operator.sol#L686](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L686)

## [G02] Array length should not be looked up in every loop of a for-loop
### Description
Storage array length checks incur an extra Gwarmaccess (100 gas) per loop. 
Store the array length in a variable and use it in the for loop helps to save gas.

### Proof of concept
```solidity
contract TestForLength {
	function testArrayLength() public {
		uint256[] memory array = new uint256[](10);
		for(uint256 i; i < array.length; ){
			++i;
		}
	}
}
contract TestForCachLength {
	function testArrayLength() public {
		uint256[] memory array = new uint256[](10);
		uint256 arrayLen = array.length;
		for(uint256 i; i < arrayLen; ){
			++i;
		}
	}
}
```
- Transaction cost of TestForLength is 23217 gas
- Transaction cost of TestForCachLength is 23200 gas 
- After the test it's possible to save 17 gas in this loop so this mean **~2 gas per loop** is saved with this optimization in the test case of a local array.

### Lines in the code
[Governance.sol#L278](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L278)

## [G03] Operatos <= or >= cost more gas than operators < or >
### Description
Change all <= / >= operators for < / > and remember to increse / decrese in consecuence to maintain the logic (example, a <= b for a < b + 1)

### Proof of concept

```solidity
contract TestMaxEqual {

	function testMaxEqual() public {
		uint256 i = 1;
		if (i >= 1){
			i++;
		}
	}
}

contract TestMax {

	function TestMax() public {
		uint256 i = 1;
		if (i > 0){
			i++;
		}
	}
}
```

- Transaction cost of TestMaxEqual is 21367 gas
- Transaction cost of TestMax is 21364 gas 
- After the test it's possible to save **3 gas per ocurrence** with this optimization.

### Lines in the code
[Operator.sol#L210](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L210)
[Operator.sol#L211](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L211)
[Operator.sol#L216](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L216)
[Operator.sol#L217](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L217)
[Operator.sol#L486](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L486)
[Operator.sol#L667](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L667)
[Operator.sol#L683](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L683)

## [G04] != 0 is cheaper than > 0
### Description
Replace all > 0 for != 0

### Lines in the code
[Governance.sol#L247](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L247)

## [G05] Variable1 = Variable1 + (-) Variable2 is cheaper in gas cost than variable1 += (-=) variable2.
### Description


### Lines in the code
[TRSRY.sol#L96](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/TRSRY.sol#L96)
[TRSRY.sol#L97](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/TRSRY.sol#L97)
[TRSRY.sol#L115](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/TRSRY.sol#L115)
[TRSRY.sol#L116](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/TRSRY.sol#L116)
[TRSRY.sol#L131](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/TRSRY.sol#L131)
[TRSRY.sol#L132](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/TRSRY.sol#L132)
[PRICE.sol#L136](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L136)
[PRICE.sol#L138](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L138)
[PRICE.sol#L222](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L222)
[VOTES.sol#L56](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/VOTES.sol#L56)
[VOTES.sol#L58](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/VOTES.sol#L58)
[BondCallback.sol#L143](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/BondCallback.sol#L143)
[BondCallback.sol#L144](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/BondCallback.sol#L144)
[Heart.sol#L103](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Heart.sol#L103)
[Governance.sol#L194](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L194)
[Governance.sol#L198](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L198)
[Governance.sol#L252](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L252)
[Governance.sol#L254](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L254)

## [G06] Using private rather than public for constants
### Description
If needed, the value can be read from the verified contract source code. 
Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, 
and not adding another entry to the method ID table.

### Lines in the code
[RANGE.sol#L65](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L65)
[PRICE.sol#L59](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L59)
[Operator.sol#L89](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L89)
[Governance.sol#L121](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L121)
[Governance.sol#L124](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L124)
[Governance.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L127)
[Governance.sol#L130](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L130)
[Governance.sol#L133](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L133)
[Governance.sol#L137](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L137)

## [G07] Don't compare boolean expressions to boolean literals
### Description
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

### Lines in the code
[Governance.sol#L223](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L223)
[Governance.sol#L306](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L306)
	
## [G08] Usage of uints/ints smaller than 32 Bytes (256 bits) incurs overhead
### Description
When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. 
Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Use a larger size then downcast where needed

### Lines in the code

[RANGE.sol#L45](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L45)
[PRICE.sol#L44](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L44)
[PRICE.sol#L47](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L47)
[PRICE.sol#L50](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L50)
[PRICE.sol#L53](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L53)
[PRICE.sol#L56](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L56)
[PRICE.sol#L59](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L59)
[PRICE.sol#L84](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L84)
[PRICE.sol#L87](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L87)
[PRICE.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L127)
[PRICE.sol#L161](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L161)
[PRICE.sol#L185](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L185)
[Operator.sol#L83](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L83)
[Operator.sol#L86](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L86)
[Operator.sol#L89](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L89)
[Operator.sol#L371](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L371)
[Operator.sol#L372](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L372)
[Operator.sol#L375](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L375)
[Operator.sol#L418](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L418)
[Operator.sol#L426](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L426)
[Operator.sol#L427](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L427)
[Operator.sol#L430](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L430)
[Operator.sol#L485](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L485)
[Operator.sol#L665](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L665)
[IOperator.sol#L13](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L13)
[IOperator.sol#L14](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L14)
[IOperator.sol#L15](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L15)
[IOperator.sol#L16](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L16)
[IOperator.sol#L17](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L17)
[IOperator.sol#L18](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L18)
[IOperator.sol#L19](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L19)
[IOperator.sol#L20](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L20)
[IOperator.sol#L31](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L31)
[IOperator.sol#L32](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L32)
[IOperator.sol#L33](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L33)

## [G09] Initialize variables with default values are not needed
### Description
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address�). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### Lines in the code

[Kernel.sol#L397](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L397)
[KernelUtils.sol#L43](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/utils/KernelUtils.sol#L43)
[KernelUtils.sol#L58](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/utils/KernelUtils.sol#L58)

Assuming than uint's less than 256 are updated to uint256.
[Operator.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L127)
[Operator.sol#L129](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L129)
[Operator.sol#403](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L403)
[Operator.sol#455](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L455)

## [G10] Using bools for storage incurs overhead
### Description
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

### Lines in the code
[Kernel.sol#L113](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L113)
[Kernel.sol#L181](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L181)
[Kernel.sol#L194](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L194)
[Kernel.sol#L197](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L197)
[RANGE.sol#L44](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L44)
[PRICE.sol#L62](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L62)
[Operator.sol#L63](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L63)
[Operator.sol#L66](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L66)
[Operator.sol#L735](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L735)
[BondCallback.sol#L24](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/BondCallback.sol#L24)
[Heart.sol#L33](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Heart.sol#L33)
[Governance.sol#L105](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L105)
[Governance.sol#L117](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L117)
[IOperator.sol#L34](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L34)

## [G11] Multiplication/division by two should use bit shifting
### Description
<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. 
The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

### Proof of concept
```solidity
contract TestDiv2 {
	function TestDivBy2 () public returns (uint256){
		uint256 i = 4;
		i = i / 2;
		return i;
	}
}

contract TestDivShift {
	function TestDivByShift () public returns (uint256){
		uint256 i = 4;
		i = i >> 1;
		return i;
	}
}
```
- Transaction cost of TestDiv2 is 21581 gas
- Transaction cost of TestDivShift is 21409 gas 
- After the test it's possible to **save 172 gas with this optimization** per ocurrence.

### Lines in the code
[Operator.sol#L372](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L372)
[Operator.sol#L419](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L419)
[Operator.sol#L420](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L420)
[Operator.sol#L427](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L427)

## [G12] Calldata vs Memory
### Description
Use calldata instead of memory in a function parameter when you are only to read the data can save gas by storing it in calldata

### Lines in the code
[PRICE.sol#L205](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L205)
[TreasuryCustodian.sol#L53](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/TreasuryCustodian.sol#L53)
[BondCallback.sol#L152](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/BondCallback.sol#L152)
[PriceConfig.sol#L45](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/PriceConfig.sol#L45)


## [G13] Use a more recent version of solidity
### Description
Use a solidity version of at least 0.8.16 to have more efficient code for checked addition and subtraction.
	
### Lines in the code
[Kernel.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L2)
[KernelUtils.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/utils/KernelUtils.sol#L2)
[TRSRY.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/TRSRY.sol#L2)
[MINTR.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/MINTR.sol#L2)
[RANGE.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L2)
[PRICE.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/PRICE.sol#L2)
[VOTES.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/VOTES.sol#L2)
[INSTR.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/INSTR.sol#L2)
[TreasuryCustodian.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/TreasuryCustodian.sol#L2)
[Operator.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L2)
[BondCallback.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/BondCallback.sol#L2)
[Heart.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Heart.sol#L2)
[PriceConfig.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/PriceConfig.sol#L2)
[Governance.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L2)
[VoterRegistration.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/VoterRegistration.sol#L2)
[IBondCallback.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/interfaces/IBondCallback.sol#L2)
[IHeart.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IHeart.sol#L2)
[IOperator.sol#L2](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/interfaces/IOperator.sol#L2)

## [G14] Using storage instead of memory for structs/arrays
### Description
When retrieving data from a memory location, assigning the data to a memory variable causes all fields of the struct/array to be read from memory, 
resulting in a Gcoldsload (2100 gas) for each field of the struct/array. When reading fields from new memory variables, they cause an extra MLOAD 
instead of a cheap stack read. Rather than declaring a variable with the memory keyword, it is much cheaper to declare a variable with the storage 
keyword and cache all fields that need to be read again in a stack variable, because the fields actually read will only result in a Gcoldsload. 
The only case where the entire struct/array is read into a memory variable is when the entire struct/array is returned by a function, 
passed to a function that needs memory, or when the array/struct is read from another store array/struct.

### Lines in the code
[Kernel.sol#L379](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L379)
[Kernel.sol#L398](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L398)
[RANGE.sol#L80](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/modules/RANGE.sol#L80)
[Operator.sol#L96](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L96)
[Operator.sol#L97](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L97)
[BondCallback.sol#L179](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/BondCallback.sol#L179)
[Governance.sol#L206](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L206)

## [G15] Tight variable packing 
### Description
Reordering the variables's declaration it's possible to save some slots. Apply the following changes.

#### Operator.sol
```diff
    /// Operator variables, defined in the interface on the external getter functions
    Status internal _status;
    Config internal _config;

    /// @notice    Whether the Operator has been initialized
-   bool public initialized;

    /// @notice    Whether the Operator is active
-   bool public active;

    /// Modules
    OlympusPrice internal PRICE;
    OlympusRange internal RANGE;
    OlympusTreasury internal TRSRY;
    OlympusMinter internal MINTR;

    /// External contracts
    /// @notice     Auctioneer contract used for cushion bond market deployments
    IBondAuctioneer public auctioneer;
    /// @notice     Callback contract used for cushion bond market payouts
    IBondCallback public callback;

    /// Tokens
    /// @notice     OHM token contract
    ERC20 public immutable ohm;
-   uint8 public immutable ohmDecimals;
    /// @notice     Reserve token contract
    ERC20 public immutable reserve;
    uint8 public immutable reserveDecimals;
+   uint8 public immutable ohmDecimals;
+   bool public initialized;
+   bool public active;
    /// Constants
    uint32 public constant FACTOR_SCALE = 1e4;
```
### Lines in the code
[Operator.sol#L59-L89](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L59-L89)

## [G16] Move variable declaration before is going to be used
### Description
It's important to declare the variable before it's use and after to the if/require conditions becase of it's possible to save gas
when the if's/require conditions are true and the execution doesn't follow.

```diff
function endorseProposal(uint256 proposalId_) external {
-   uint256 userVotes = VOTES.balanceOf(msg.sender);

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
+   uint256 userVotes = VOTES.balanceOf(msg.sender);
    userEndorsementsForProposal[proposalId_][msg.sender] = userVotes;
    totalEndorsementsForProposal[proposalId_] += userVotes;

    emit ProposalEndorsed(proposalId_, msg.sender, userVotes);
}
```

```diff
function vote(bool for_) external {
-   uint256 userVotes = VOTES.balanceOf(msg.sender);

    if (activeProposal.proposalId == 0) {
        revert NoActiveProposalDetected();
    }

    if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
        revert UserAlreadyVoted();
    }
	
+   uint256 userVotes = VOTES.balanceOf(msg.sender);
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
### Lines in the code
[Governance.sol#L180-L201](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L180-L201)
[Governance.sol#L240-L262](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Governance.sol#L240-L262)

## [G17] Refactoring code
### Description
In the following case it's possible to save gas checking the common condition once instead of twice. 

```diff
-if (
-    uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&
-    _status.high.count >= config_.regenThreshold
-) {
-    _regenerate(true);
-}
-if (
-    uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&
-    _status.low.count >= config_.regenThreshold
-) {
-    _regenerate(false);
-}

+if (uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait))
+{
+	if (_status.high.count >= config_.regenThreshold)
+	{
+		_regenerate(true);
+	}
+	if (_status.low.count >= config_.regenThreshold) 
+	{
+		_regenerate(false);
+	}
+}

```

### Lines in the code
[Operator.sol#L209-L220](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L209-L220)

## [G18] Use unchecked when it's not possible to overflow
### Description
The default “checked” behavior costs more gas when adding/diving/multiplying, because under-the-hood those checks are implemented as a series of opcodes that, prior to performing the actual arithmetic,
check for under/overflow and revert if it is detected. if it can statically be determined there is no possible way for your arithmetic to under/overflow (such as a condition in an if statement),
surrounding the arithmetic in an unchecked block will save gas.

For all for-loops in the code it is possible to change as the following example.

```diff
for (uint256 i;i < X;){
	-- code --
	unchecked
	{
		++i;
	}
}
```

### Proof of concept

```solidity
contract TestWithoutUnchecked {
	function Test() public {
		for(uint256 i; i < 10; ){
			++i;
		}
	}
}

contract TestWitUnchecked {
	function Test() public {
		for(uint256 i; i < 10; ){
			unchecked {
				++i;
			}
		}
	}
}

``` 
- Transaction cost of TestWithoutUnchecked is 22958 gas
- Transaction cost of TestWitUnchecked is 21728 gas 
- After the test it's possible to save 1230 gas in this loop so this mean **123 gas per loop is saved with this optimization**.

### Lines in the code
[Operator.sol#L487-L488](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L487-L488)


## [G19] Internal functions only called once can be inlined to save gas

### Description
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

### Operator._addObservation
[Operator.sol#L652](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/policies/Operator.sol#L652)
### Kernel._installModule
[Kernel.sol#L266-L277](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L266-L277)
### Kernel._upgradeModule
[Kernel.sol#L279-L293](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L279-L293)
### Kernel._activatePolicy
[Kernel.sol#L295-L323](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L295-L323)
### Kernel._deactivatePolicy
[Kernel.sol#L325-L346](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L325-L346)
### Kernel._migrateKernel
[Kernel.sol#L351-L372](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L351-L372)
### Kernel._reconfigurePolicies
[Kernel.sol#L378-L389](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L378-L389)
### Kernel._pruneFromDependents
[Kernel.sol#L409-L432](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L409-L432)

## [G20] Remove unused functions

### Description
Remove the following functions in Kernel.sol (getModuleAddress) to save gas due to it's internal and it's not used inside the contract. 

### Lines in the code
[Kernel.sol#L131-L135](https://github.com/code-423n4/2022-08-olympus/blob/277535739c465c75d37c33d706ab76365df2aade/src/Kernel.sol#L131-L135)

