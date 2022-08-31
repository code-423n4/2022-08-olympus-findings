### [G-01] ++i costs less gas compared to i++

++i costs **about 5 gas less per iteration** compared to i++ for unsigned integer. 
Same thing for decremental with -- operator.
This statement is true even with the optimizer enabled.
Summarized my results where i is used in a loop, is unsigned integer, and you safely can be changed to ++i without changing any behavior,

** Most places are using best practice (++i and unchecked where possible) - good job
Yet I've found 5 locations that can do better:

```
src/policies/Operator.sol:
  487              price_ = price_ / 10;
  488:             decimals++;
  489          }

  669                  _status.low.observations[regen.nextObservation] = true;
  670:                 _status.low.count++;
  671              }

  674                  _status.low.observations[regen.nextObservation] = false;
  675:                 _status.low.count--;
  676              }

  685                  _status.high.observations[regen.nextObservation] = true;
  686:                 _status.high.count++;
  687              }
  
  690                  _status.high.observations[regen.nextObservation] = false;
  691:                 _status.high.count--;
  692              }
  
```

---------------------------------------------------------------------------


### [G-02] An arrays length should be cached to save gas in for-loops

An array’s length should be cached to save gas in for-loops
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset).
Caching the array length in the stack saves around **3 gas per iteration**.

I've found only 1 location to improve:

```
src/policies/Governance.sol:
  277  
  278:         for (uint256 step; step < instructions.length; ) {
  279              kernel.executeAction(instructions[step].action, instructions[step].target);
  
```

---------------------------------------------------------------------------


### [G-03] Using default values is cheaper than assignment

If a variable is not set/initialized, it is assumed to have the default value 0 for uint, and false for boolean.
Explicitly initializing it with its default value is an anti-pattern and wastes gas.
For example: ```uint8 i = 0;``` should be replaced with ```uint8 i;```

I've found 3 locations in 2 files:

```
src/Kernel.sol:
  396          uint256 reqLength = requests_.length;
  397:         for (uint256 i = 0; i < reqLength; ) {
  398              Permissions memory request = requests_[i];

src/utils/KernelUtils.sol:
  42  
  43:     for (uint256 i = 0; i < 5; ) {
  44          bytes1 char = unwrapped[i];

  57  
  58:     for (uint256 i = 0; i < 32; ) {
  59          bytes1 char = unwrapped[i];


```

---------------------------------------------------------------------------


### [G-04] != 0 is cheaper than > 0

!= 0 costs less gas compared to > 0 for unsigned integers even when optimizer enabled
All of the following findings are uint (E&OE) so >0 and != have exactly the same effect.
** saves 6 gas ** each

I've found only 1 place to improve - good job on all other uses of !=

```
src/policies/Governance.sol:
  246  
  247:         if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
  248              revert UserAlreadyVoted();
```

---------------------------------------------------------------------------


### [G-05] Upgrade pragma to 0.8.16 to save gas

Across the whole solution, the declared pragma is 0.8.15 (good!)
Upgrading to 0.8.16 may result in lower gas uses.

Source:
```
According to the release note of 0.8.16: https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/
".. there are several minor bug fixes and improvements like more gas-efficient overflow checks for addition and subtraction."
```


---------------------------------------------------------------------------


* Custom errors save gas - already using custom errors all over the project - good job!!
* Using immutables (for vars that are only in constructor) seems find throughout the project
