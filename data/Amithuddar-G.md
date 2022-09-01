1)It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {  
 

File: 2022-08-olympus\src\Kernel.sol
  397,14:         for (uint256 i = 0; i < reqLength; ) {

File: 2022-08-olympus\src\utils\KernelUtils.sol
  43,10:     for (uint256 i = 0; i < 5; ) {
  58,10:     for (uint256 i = 0; i < 32; ) { 

2)<ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
The overheads outlined below are PER LOOP, 

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset
  

File: 2022-08-olympus\src\policies\Governance.sol
 
  278,47:         for (uint256 step; step < instructions.length; ) {


3)Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code.
Savings are due to the compiler not having to create non-payable getter
functions for deployment calldata, and not adding another entry to the method ID table

File: 2022-08-olympus\src\modules\PRICE.sol
  59,11:     uint8 public constant decimals = 18;
  

File: 2022-08-olympus\src\modules\RANGE.sol
  65,13:     uint256 public constant FACTOR_SCALE = 1e4;

File: 2022-08-olympus\src\policies\Governance.sol
  121,13:     uint256 public constant SUBMISSION_REQUIREMENT = 100;
  124,13:     uint256 public constant ACTIVATION_DEADLINE = 2 weeks;
  127,13:     uint256 public constant GRACE_PERIOD = 1 weeks;
  130,13:     uint256 public constant ENDORSEMENT_THRESHOLD = 20;
  133,13:     uint256 public constant EXECUTION_THRESHOLD = 33;
  137,13:     uint256 public constant EXECUTION_TIMELOCK = 3 days;

File: 2022-08-olympus\src\policies\Operator.sol
  89,12:     uint32 public constant FACTOR_SCALE = 1e4;
  
4)X = X + Y IS CHEAPER THAN X += Y 	  

File: 2022-08-olympus\src\modules\PRICE.sol
  136,28:             _movingAverage += (currentPrice - earliestPrice) / numObs;
  222,19:             total += startObservations_[i];

File: 2022-08-olympus\src\modules\TRSRY.sol
  96,41:         reserveDebt[token_][msg.sender] += amount_;
  97,27:         totalDebt[token_] += amount_;
  131,50:         if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;

File: 2022-08-olympus\src\modules\VOTES.sol
  58,28:             balanceOf[to_] += amount_;

File: 2022-08-olympus\src\policies\BondCallback.sol
  143,35:         _amountsPerMarket[id_][0] += inputAmount_;
  144,35:         _amountsPerMarket[id_][1] += outputAmount_;

File: 2022-08-olympus\src\policies\Governance.sol
  198,51:         totalEndorsementsForProposal[proposalId_] += userVotes;
  252,60:             yesVotesForProposal[activeProposal.proposalId] += userVotes;
  254,59:             noVotesForProposal[activeProposal.proposalId] += userVotes;

File: 2022-08-olympus\src\policies\Heart.sol
  103,18:         lastBeat += frequency();
  

  


  