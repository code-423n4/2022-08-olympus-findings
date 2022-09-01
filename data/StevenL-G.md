First gas optimization - OR (||) conditions cost less than their equivalent AND (&&) conditions ("not(something is false) cost less than ("everything is true")

Remember that the equivalent of (a && b) is !(!a || !b), even with the 10k Optimizer enabled: OR (||) (conditions cost less than their equivalent AND (&&) conditions.

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol
61: -} else if (instruction.action == Actions.ChangeExecutor && i != length - 1) {
61: +} } else if (!(instruction.action != Actions.ChangeExecutor || i = length - 1) {

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/RANGE.sol
133: -if (capacity_ < _range.high.threshold && _range.high.active) {
133: +if !(!(capacity_ < _range.high.threshold) || !_range.high.active) {

145: -if (capacity_ < _range.low.threshold && _range.low.active) {
145: +if !(!(capacity_ < _range.low.threshold) || !_range.low.active) {

221: -if (market_ == type(uint256).max && marketCapacity_ != 0) revert RANGE_InvalidParams();
221: +if (!(market_ != type(uint256).max || marketCapacity_ = 0) revert RANGE_InvalidParams();

Second gas optimization - ++ should be unchecked, when it is not possible for them to overflow

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/INSTR.sol
it's hardly to believe that a cache for storing a list of instructions to be executed in the future, can get greater than uint256 and overflow.
44: -uint256 instructionsId = ++totalInstructions;
44: +unchecked { uint256 instructionsId = ++totalInstructions; }

Third gas optimization - Usage of uints/ints smaller than 32 bytes (256 bits) incurs in overhead

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol
44: uint32 public nextObsIndex;
47: uint32 public numObservations;
50: uint48 public observationFrequency;
53: uint48 public movingAverageDuration;
56: uint48 public lastObservationTime;

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol
83: uint8 public immutable ohmDecimals;
86: uint8 public immutable reserveDecimals;

Fourth gas optimization - No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol
43: -for (uint256 i = 0; i < 5; ) {
43: +for (uint256 i; i < 5; ) {

58: -for (uint256 i = 0; i < 32; ) {
58: +for (uint256 i; i < 32; ) {

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol
397: -for (uint256 i = 0; i < reqLength; ) {
397: +for (uint256 i; i < reqLength; ) {

Fifth gas optimization - <=/>= cost less gas than </> 

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol
43: -for (uint256 i = 0; i < 5; ) {
43: +for (uint256 i = 0; i <= 4; ) {

58: -for (uint256 i = 0; i < 32; ) {
58: +for (uint256 i = 0; i <= 31; ) {