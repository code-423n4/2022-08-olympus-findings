* Redundant boolean checks: Governance.sol::223,306

* BondCallback.sol::160 The remaining balance will always be 0, therefore the priorBalances can simply be set to 0

* Governance.sol::193 The `previousEndorsement` variable is only used once, the following line can simply read from userEndorsementsForProposal without declaring a stack variable.

* INSTR.sol::48 this check can be moved up to the second line of the function, to avoid spending extra gas in the case of a revert.

* TRSRY.sol::122 in the `setDebt` function, instead of storing `oldDebt` and using an if to conditionally handle the debt change, Simply subtract the `oldDebt` from the `totalDebt` and add the new debt amount to the `totalDebt`.

* PRICE.sol::177 no need to declare a `currentPrice` variable only to return it on the next line, instead just return the computed value directly.

* Operator.sol::299 Rather than transferring ohm from the `msg.sender` to the `operator` contract and then burning it from the `operator` contract, the `MINTR` can burn the ohm directly from the `msg.sender`.

* Variables should not be initialized to defaults (uint256 default is 0):
  Kernel.sol::397 => for (uint256 i = 0; i < reqLength; ) {
  utils/KernelUtils.sol::43 => for (uint256 i = 0; i < 5; ) {
  utils/KernelUtils.sol::58 => for (uint256 i = 0; i < 32; ) {

* Length of array should be computed outside of for-loop:
  Kernel.sol::300 => getPolicyIndex[policy_] = activePolicies.length - 1;
  Kernel.sol::304 => uint256 depLength = dependencies.length;
  Kernel.sol::310 => getDependentIndex[keycode][policy_] = moduleDependents[keycode].length - 1;
  Kernel.sol::334 => Policy lastPolicy = activePolicies[activePolicies.length - 1];
  Kernel.sol::352 => uint256 keycodeLen = allKeycodes.length;
  Kernel.sol::361 => uint256 policiesLen = activePolicies.length;
  Kernel.sol::380 => uint256 depLength = dependents.length;
  Kernel.sol::396 => uint256 reqLength = requests_.length;
  Kernel.sol::411 => uint256 depcLength = dependencies.length;
  Kernel.sol::418 => Policy lastPolicy = dependents[dependents.length - 1];  modules/INSTR.sol::43 => uint256 length = instructions_.length;
  modules/INSTR.sol::48 => if (length == 0) revert INSTR_InstructionsCannotBeEmpty();
  modules/INSTR.sol::50 => for (uint256 i; i < length; ) {
  modules/INSTR.sol::61 => } else if (instruction.action == Actions.ChangeExecutor && i != length - 1) {
  modules/PRICE.sol::201 => /// @param  startObservations_ - Array of observations to initialize the moving average with. Must be of length numObservations.
  modules/PRICE.sol::212 => uint256 numObs = observations.length;
  modules/PRICE.sol::215 => if (startObservations_.length != numObs || lastObservationTime_ > uint48(block.timestamp))
  policies/BondCallback.sol::155 => uint256 len = tokens_.length;
  policies/Governance.sol::188 => if (instructions.length == 0) {
  policies/Governance.sol::278 => for (uint256 step; step < instructions.length; ) {
  policies/PriceConfig.sol::41 => /// @param startObservations_   Array of observations to initialize the moving average with. Must be of length numObservations.
  policies/TreasuryCustodian.sol::58 => uint256 len = tokens_.length;  

* != is more efficient than > 0 for uint comparisons:
  policies/Governance.sol::247 => if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
  test/modules/PRICE.t.sol::101 => change = int256(uint256(keccak256(abi.encodePacked(nonce, i)))) % int256(1000);
  test/modules/PRICE.t.sol::128 => change = int256(uint256(keccak256(abi.encodePacked(nonce, i)))) % int256(1000);
  test/policies/PriceConfig.t.sol::124 => change = int256(uint256(keccak256(abi.encodePacked(nonce, i)))) % int256(1000);

* Switching from division/multiplication to right-shift/left-shift can save gas:
  policies/Operator.sol::372 => int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
  policies/Operator.sol::419 => uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
  policies/Operator.sol::420 => uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
  policies/Operator.sol::427 => int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
  policies/Operator.sol::786 => ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /  
