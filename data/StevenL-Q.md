First QA issue - Non-assembly method available

assembly { size := extcodesize() } => uint256 size = address().code.length

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/utils/KernelUtils.sol
34: size := extcodesize(target_)

Second QA issue - Constant redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/modules/PRICE.sol
59: uint8 public constant decimals = 18;

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol
121: uint256 public constant SUBMISSION_REQUIREMENT = 100;
130: uint256 public constant ENDORSEMENT_THRESHOLD = 20;
133: uint256 public constant EXECUTION_THRESHOLD = 33;

Third QA issue - Variable names that consist of all capital letters should be reserved for constant/immutable variables

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Operator.sol
69: OlympusPrice internal PRICE;
70: OlympusRange internal RANGE;
71: OlympusTreasury internal TRSRY;
72: OlympusMinter internal MINTR;

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/BondCallback.sol
29: OlympusTreasury public TRSRY;
30: OlympusMinter public MINTR;

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Heart.sol
45: OlympusPrice internal PRICE;

https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/policies/Governance.sol
56: OlympusInstructions public INSTR;
57: OlympusVotes public VOTES;

