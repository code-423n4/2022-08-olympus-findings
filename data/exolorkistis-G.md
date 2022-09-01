[1] ``<ARRAY>.LENGTH`` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A`` FOR``-LOOP

The overheads outlined below are *PER LOOP*, excluding the first loop

-storage arrays incur a Gwarmaccess (100 gas)
-memory arrays use MLOAD (3 gas)
-calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset.


*There are 5 instances of this issue:*

```
File : 2022-08-olympus/src/Kernel.sol

306: for (uint256 i; i < depLength; ) {

413: for (uint256 i; i < depcLength; ) {

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L306](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L306)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L413](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L413)

```
File :2022-08-olympus/src/policies/TreasuryCustodian.sol

59:   for (uint256 j; j < len; ) {

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L59](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/TreasuryCustodian.sol#L59)

```
File: 2022-08-olympus/src/policies/BondCallback.sol

156:   for (uint256 i; i < len; ) {

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L156](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L156)

```

File: 2022-08-olympus/src/policies/Governance.sol

278:  for (uint256 step; step < instructions.length; ) {

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L278](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L278)

[2] USING  ``PRIVATE`` RATHER THAN ``PUBLIC`` FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

*There are 9 instances of this issue:*

```
File: 2022-08-olympus/src/modules/RANGE.sol

65:  uint256 public constant FACTOR_SCALE = 1e4;

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L65](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L65)

```
File: 2022-08-olympus/src/modules/PRICE.sol

59: uint8 public constant decimals = 18;

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59)

```
File:2022-08-olympus/src/policies/Operator.sol

89:  uint32 public constant FACTOR_SCALE = 1e4;

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89)

```
File: 2022-08-olympus/src/policies/Governance.sol

121:  uint256 public constant SUBMISSION_REQUIREMENT = 100;

124:   uint256 public constant ACTIVATION_DEADLINE = 2 weeks;

127:   uint256 public constant GRACE_PERIOD = 1 weeks;

130:    uint256 public constant ENDORSEMENT_THRESHOLD = 20;

133:   uint256 public constant EXECUTION_THRESHOLD = 33;

137:    uint256 public constant EXECUTION_TIMELOCK = 3 days;

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L121](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L121)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L124](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L124)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L127)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L130](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L130)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L133](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L133)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L137](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L137)



[3]  ``PUBLIC`` FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED ``EXTERNAL`` INSTEAD

Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from ``external`` to ``public`` and can save gas by doing so.

*There are 5 instances of this issue:*

```
File: 2022-08-olympus/src/Kernel.sol

439:  function grantRole(Role role_, address addr_) public onlyAdmin {

451:   function revokeRole(Role role_, address addr_) public onlyAdmin {

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L439)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L451](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L451)

```
File: 2022-08-olympus/src/modules/TRSRY.sol

75:  function withdrawReserves(
76:        address to_,
77:        ERC20 token_,
78:        uint256 amount_
79:    ) public {

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L75-L79](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L75-L79)

```
File: 2022-08-olympus/src/modules/MINTR.sol

37:  function burnOhm(address from_, uint256 amount_) public permissioned {

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L37](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L37)

```
File: 2022-08-olympus/src/modules/INSTR.sol

37: function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L37](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L37)


[4] DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS


``if (<x> == true)`` => ``if(<x>)`` , ``if (<x> == false)`` => ``if (!<x>)``

*There are 2 instances of this issue:*

```

File: 2022-08-olympus/src/policies/Governance.sol

223:   if (proposalHasBeenActivated[proposalId_] == true) {

306:     if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L223](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L223)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L306](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L306)


[5] USAGE OF ``UINTS/INTS`` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

*There are 92 instances of this issue*

```

File: 2022-08-olympus/src/Kernel.sol

100:  function VERSION() external pure virtual returns (uint8 major, uint8 minor) {}   #1

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L100](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L100)

```
File: 2022-08-olympus/src/modules/MINTR.sol

25: function VERSION() external pure override returns (uint8 major, uint8 minor) {      #2

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L25](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L25)


```

File: 2022-08-olympus/src/modules/TRSRY.sol

51:  function VERSION() external pure override returns (uint8 major, uint8 minor) {     #3

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L51](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L51)

```

File: 2022-08-olympus/src/modules/RANGE.sol

45:  uint48 lastActive;           #4

85:  lastActive: uint48(block.timestamp),       #5

92:  lastActive: uint48(block.timestamp),       #6

136:  _range.high.lastActive = uint48(block.timestamp);        #7

148:  _range.low.lastActive = uint48(block.timestamp);        #8

191:  lastActive: uint48(block.timestamp),                    #9

200:   lastActive: uint48(block.timestamp),                   #10

115:  function VERSION() external pure override returns (uint8 major, uint8 minor) {      #11

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L45](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L45)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L85](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L85)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L92](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L92)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L136](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L136)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L148](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L148)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L191](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L191)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L200](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L200)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L115](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L115)

```

File: 2022-08-olympus/src/modules/PRICE.sol

44:   uint32 public nextObsIndex;              #12

47:   uint32 public numObservations;              #13

97:    numObservations = uint32(movingAverageDuration_ / observationFrequency_);        #14

127:   uint32 numObs = numObservations;                       #15

185:   uint32 lastIndex = nextObsIndex == 0 ? numObservations - 1 : nextObsIndex - 1;       #16

257:  numObservations = uint32(newObservations);                  #17

289:   numObservations = uint32(newObservations);                 #18

50:    uint48 public observationFrequency;                    #19

53:    uint48 public movingAverageDuration;                  #20

56:    uint48 public lastObservationTime;                    #21

75:     uint48 observationFrequency_,                         #22

76:      uint48 movingAverageDuration_                         #23

143:      lastObservationTime = uint48(block.timestamp);        #24

205:     function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)      #25

215:       if (startObservations_.length != numObs || lastObservationTime_ > uint48(block.timestamp))     #26

    
240:      function changeMovingAverageDuration(uint48 movingAverageDuration_) external permissioned {     #27

266:      function changeObservationFrequency(uint48 observationFrequency_) external permissioned {       #28

59:        uint8 public constant decimals = 18;                     #29

84:        uint8 ohmEthDecimals = _ohmEthPriceFeed.decimals();        #30

87:        uint8 reserveEthDecimals = _reserveEthPriceFeed.decimals();         #31

113:       function VERSION() external pure override returns (uint8 major, uint8 minor) {        #32

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L44](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L44)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L47](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L47)
 
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L97](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L97)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L127)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L185](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L185)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L257](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L257)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L289](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L289)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L50](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L50)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L53](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L53)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L56](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L56)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L75-L76](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L75-L76)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L143](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L143)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L205)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L215](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L215)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L240](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L240)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L266](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L266)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L59)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L84](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L84)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L87](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L87)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L113](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L113)


```

File: 2022-08-olympus/src/modules/VOTES.sol

27: function VERSION() external pure override returns (uint8 major, uint8 minor) {       #33

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L27](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/VOTES.sol#L27)
 

```

File: 2022-08-olympus/src/modules/INSTR.sol

28:  function VERSION() public pure override returns (uint8 major, uint8 minor) {        #34

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L28](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/INSTR.sol#L28)
 
 
```

File: 2022-08-olympus/src/policies/Operator.sol


83:  uint8 public immutable ohmDecimals;                #35

86:  uint8 public immutable reserveDecimals;.             #36

375:  uint256 oracleScale = 10**uint8(int8(PRICE.decimals()) - priceDecimals);           #37

377:   uint8(                                                                              #38
378:   36 + scaleAdjustment + int8(reserveDecimals) - int8(ohmDecimals) - priceDecimals
379:   );

418:    uint8 oracleDecimals = PRICE.decimals();                     #39

430:     uint256 oracleScale = 10**uint8(int8(oracleDecimals) - priceDecimals);          #40

432:      uint8(                                                                          #41
433:      36 + scaleAdjustment + int8(ohmDecimals) - int8(reserveDecimals) - priceDecimals
434:      );

128:      lastRegen: uint48(block.timestamp),             #42

210:      uint48(block.timestamp) >= RANGE.lastActive(true) + uint48(config_.regenWait) &&       #43
         
216:      uint48(block.timestamp) >= RANGE.lastActive(false) + uint48(config_.regenWait) &&        #44

403:       vesting: uint48(0), // Instant swaps                            #45

404:        conclusion: uint48(block.timestamp + config_.cushionDuration),       #46

708:         _status.high.lastRegen = uint48(block.timestamp);                 #47

720:        _status.low.lastRegen = uint48(block.timestamp);                  #48

89:         uint32 public constant FACTOR_SCALE = 1e4;                           #49

97:          uint32[8] memory configParams                     #50

106:          if (configParams[2] < uint32(10_000)) revert Operator_InvalidParams();          #51

108:           if (configParams[3] < uint32(1 hours) || configParams[3] > configParams[1])     #52

116:             configParams[7] == uint32(0)                  #53

127:            count: uint32(0),                           #54

129:             nextObservation: uint32(0),                  #55

516:          function setCushionFactor(uint32 cushionFactor_) external onlyRole("operator_policy") {       #56

528:           uint32 duration_,                #57
529:        uint32 debtBuffer_,                   #58
530:        uint32 depositInterval_              #59

535:        if (debtBuffer_ < uint32(10_000)) revert Operator_InvalidParams();          #60
536:        if (depositInterval_ < uint32(1 hours) || depositInterval_ > duration_)       #61

548:       function setReserveFactor(uint32 reserveFactor_) external onlyRole("operator_policy") {       #62

560:        uint32 wait_,               #63
561:        uint32 threshold_,        #64
562:        uint32 observe_              #65

665:       uint32 observe = _config.regenObserve;          #66

705:        _status.high.count = uint32(0);                   #67

707:         _status.high.nextObservation = uint32(0);         #68

717:       _status.low.count = uint32(0);                    #69

719:       _status.low.nextObservation = uint32(0);              #70
 
```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L83](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L83)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L86](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L86)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L375](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L375)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L377-L379](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L377-L379)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L418](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L418)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L430](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L430)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L432-L434](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L432-L434)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L128](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L128)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L210](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L210)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L216](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L216)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L403-L404](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L403-L404)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L708](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L708)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L720](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L720)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L89)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L97](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L97)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L106](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L106)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L108](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L108)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L116](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L116)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L127)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L129](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L129)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L516](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L516)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L528-L530](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L528-L530)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L535-L536](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L535-L536)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L548](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L548)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L560-L562](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L560-L562)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L665](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L665)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L705](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L705)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L707](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L707)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L717](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L717)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L719](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L719)

```

File: 2022-08-olympus/src/policies/PriceConfig.sol

45:  function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)               #71

58:   function changeMovingAverageDuration(uint48 movingAverageDuration_)                               #72

69:   function changeObservationFrequency(uint48 observationFrequency_)                                 #73

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L45)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L58](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L58)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L69](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/PriceConfig.sol#L69)


```

File: 2022-08-olympus/src/policies/interfaces/IOperator.sol

13:  uint32 cushionFactor;       #74
14:  uint32 cushionDuration;      #75
15:  uint32 cushionDebtBuffer;    #76
16:  uint32 cushionDepositInterval;      #77
17:  uint32 reserveFactor;            #78
18:  uint32 regenWait;              #79
19:  uint32 regenThreshold;          #80
20:  uint32 regenObserve;            #81


31:  uint32 count;                    #82
32:  uint48 lastRegen;                 #83
33:  uint32 nextObservation;         #84

 
85:  function setCushionFactor(uint32 cushionFactor_) external;       #85


93:   uint32 duration_,                           #86
94:   uint32 debtBuffer_,                           #87
95:   uint32 depositInterval_                         #88

101:  function setReserveFactor(uint32 reserveFactor_) external;           #89

110:  uint32 wait_,                       #90
111:  uint32 threshold_,                  #91
112:  uint32 observe_                    #92

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L13-L20](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L13-L20)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L31-L33](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L31-L33)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L85](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L85)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L93-L95](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L93-L95)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L101](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L101)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L110-L112](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/interfaces/IOperator.sol#L110-L112)


[6]  USING ``STORAGE`` INSTEAD OF ``MEMORY`` FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a ``memory`` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional ``MLOAD`` rather than a cheap stack read. Instead of declearing the variable with the ``memory`` keyword, declaring the variable with the ``storage`` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a ``memory`` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires ``memory``, or if the array/struct is being read from another ``memory`` array/struct

*There are 3 instances of this issue:*

```

File: 2022-08-olympus/src/policies/Operator.sol

394: IBondAuctioneer.MarketParams memory params = IBondAuctioneer.MarketParams({

440:  Config memory config_ = _config;

446:   IBondAuctioneer.MarketParams memory params = IBondAuctioneer.MarketParams({

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L394](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L394)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L440](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L440)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L446](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L446)

[7]  DIVISION BY TWO SHOULD USE BIT SHIFTING

``<x> / 2`` is the same as ``<x> >>1``. The ``DIV`` opcode costs 5 gas, whereas ``SHR`` only costs 3 gas

*There are 2 instances of this issue:*

```

File:  2022-08-olympus/src/policies/Operator.sol

372:   int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);

427:   int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);


```
 [https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L372](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L372)

 [https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L427](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L427)

[8]    MULTIPLE ACCESSES OF A MAPPING/ARRAY SHOULD USE A LOCAL VARIABLE CACHE

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping’s value in a local ``storage`` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Caching an array’s struct avoids recalculating the array offsets into memory

*There are 37 instances of this issue: *

```

File: 2022-08-olympus/src/modules/TRSRY.sol

69:   withdrawApproval[withdrawer_][token_] = amount_;

143:   uint256 approval = withdrawApproval[withdrawer_][token_];

149:   withdrawApproval[withdrawer_][token_] = approval - amount_;


60: return token_.balanceOf(address(this)) + totalDebt[token_];

97:  totalDebt[token_] += amount_;

116:    totalDebt[token_] -= received;

131:    if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;

132:    else totalDebt[token_] -= oldDebt - amount_;


106:    if (reserveDebt[token_][msg.sender] == 0) revert TRSRY_NoDebtOutstanding();

115:     reserveDebt[token_][msg.sender] -= received;

127:      uint256 oldDebt = reserveDebt[token_][debtor_];

129:      reserveDebt[token_][debtor_] = amount_;

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L69](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L69)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L143](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L143)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L149](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L149)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L60](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L60)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L97](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L97)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L116](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L116)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131-L132](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131-L132)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L106](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L106)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L115](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L115)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L127](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L127)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L129](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L129)


```

File: 2022-08-olympus/src/policies/BondCallback.sol

88:   approvedMarkets[teller_][id_] = true;

106:   if (!approvedMarkets[msg.sender][id_]) revert Callback_MarketNotSupported(id_);

143:   _amountsPerMarket[id_][0] += inputAmount_;

144:   _amountsPerMarket[id_][1] += outputAmount_;

179:    uint256[2] memory marketAmounts = _amountsPerMarket[id_];

114:    if (quoteToken.balanceOf(address(this)) < priorBalances[quoteToken] + inputAmount_)

141:    priorBalances[quoteToken] = quoteToken.balanceOf(address(this));
142:      priorBalances[payoutToken] = payoutToken.balanceOf(address(this));

160:    priorBalances[token] = token.balanceOf(address(this));

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L88](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L88)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L106](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L106)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143-L144](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L143-L144)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L179](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L179)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L114](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L114)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L141-L142](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L141-L142)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L160](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L160)


```

File: 2022-08-olympus/src/policies/Governance.sol


168:   getProposalMetadata[proposalId] = ProposalMetadata(

206:   ProposalMetadata memory proposal = getProposalMetadata[proposalId_];

198:     totalEndorsementsForProposal[proposalId_] += userVotes;

217:    (totalEndorsementsForProposal[proposalId_] * 100) <

193:    uint256 previousEndorsement = userEndorsementsForProposal[proposalId_][msg.sender];

223:      if (proposalHasBeenActivated[proposalId_] == true) {

233:     proposalHasBeenActivated[proposalId_] = true;

252:      yesVotesForProposal[activeProposal.proposalId] += userVotes;

266:       uint256 netVotes = yesVotesForProposal[activeProposal.proposalId] -

254:      noVotesForProposal[activeProposal.proposalId] += userVotes;

267:      noVotesForProposal[activeProposal.proposalId];

247:      if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {

257:       userVotesForProposal[activeProposal.proposalId][msg.sender] = userVotes;

296:       uint256 userVotes = userVotesForProposal[proposalId_][msg.sender];

306:       if (tokenClaimsForProposal[proposalId_][msg.sender] == true) {

310:        tokenClaimsForProposal[proposalId_][msg.sender] = true;

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L168](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L168)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L206](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L206)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L198](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L198)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L217](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L217)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L193](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L193)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L223](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L223)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L233](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L233)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L252](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L252)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L266](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L266)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L254](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L254)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L267](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L267)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L247](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L247)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L257](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L257)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L296](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L296)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L306](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L306)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L310](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L310)


[9]  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

*There are 6 instances of this issue:*

```
File: 2022-08-olympus/src/modules/TRSRY.sol

96:  reserveDebt[token_][msg.sender] += amount_;

97:   totalDebt[token_] += amount_;
 
131:   if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L96-L97](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L96-L97)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L131)


```

File: 2022-08-olympus/src/modules/PRICE.sol


136:   _movingAverage += (currentPrice - earliestPrice) / numObs;

138:     _movingAverage -= (earliestPrice - currentPrice) / numObs;

```


[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L136)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L138](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/PRICE.sol#L138)


```

File: 2022-08-olympus/src/policies/Heart.sol

103: lastBeat += frequency();

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L103)


[10] CHEAPER TO SPLIT STRUCT IF ONLY PART OF IT IS UPDATED FREQUENTLY

The proposalId field is updated frequently so it should be in a separate struct rather than re-writing the whole struct every time

*There is 1 instance of this issue:*


``` 

File: 2022-08-olympus/src/policies/Governance.sol

167:  uint256 proposalId = INSTR.store(instructions_);


```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L167](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Governance.sol#L167)


[11]  AMOUNTS SHOULD BE CHECKED FOR 0 BEFORE CALLING A TRANSFER

Checking non-zero transfer values can avoid an expensive external call and save gas.

I suggest adding a non-zero-value check here:

*There are 8 instances of this issue:*



```

File: 2022-08-olympus/src/policies/Heart.sol

111:   function _issueReward(address to_) internal {
112:   rewardToken.safeTransfer(to_, reward);
113:    emit RewardIssued(to_, reward);

```
[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L111-L113](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L111-L113)

```
File: 2022-08-olympus/src/policies/BondCallback.sol

124:    payoutToken.safeTransfer(msg.sender, outputAmount_);

159:      token.safeTransfer(address(TRSRY), balance);

```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L124](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L124)


[https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L159](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L159)


```

File: 2022-08-olympus/src/modules/TRSRY.sol


82:  token_.safeTransfer(to_, amount_);

99:  token_.safeTransfer(msg.sender, amount_);

110:   token_.safeTransferFrom(msg.sender, address(this), amount_);


```

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L82](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L82)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L99](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L99)

[https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L110](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/TRSRY.sol#L110)







 
 
 
 
 


 
 
 
 
 
 
 

 
 
 

































