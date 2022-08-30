# c4udit Report

## Files analyzed

- 2022-08-olympus\src\Kernel.sol
- 2022-08-olympus\src\external\OlympusERC20.sol
- 2022-08-olympus\src\interfaces\AggregatorV2V3Interface.sol
- 2022-08-olympus\src\interfaces\IBondAggregator.sol
- 2022-08-olympus\src\interfaces\IBondAuctioneer.sol
- 2022-08-olympus\src\interfaces\IBondCallback.sol
- 2022-08-olympus\src\interfaces\IBondTeller.sol
- 2022-08-olympus\src\interfaces\IWETH9.sol
- 2022-08-olympus\src\libraries\FullMath.sol
- 2022-08-olympus\src\libraries\TransferHelper.sol
- 2022-08-olympus\src\modules\INSTR.sol
- 2022-08-olympus\src\modules\MINTR.sol
- 2022-08-olympus\src\modules\PRICE.sol
- 2022-08-olympus\src\modules\RANGE.sol
- 2022-08-olympus\src\modules\TRSRY.sol
- 2022-08-olympus\src\modules\VOTES.sol
- 2022-08-olympus\src\policies\BondCallback.sol
- 2022-08-olympus\src\policies\Governance.sol
- 2022-08-olympus\src\policies\Heart.sol
- 2022-08-olympus\src\policies\Operator.sol
- 2022-08-olympus\src\policies\PriceConfig.sol
- 2022-08-olympus\src\policies\TreasuryCustodian.sol
- 2022-08-olympus\src\policies\VoterRegistration.sol
- 2022-08-olympus\src\policies\interfaces\IHeart.sol
- 2022-08-olympus\src\policies\interfaces\IOperator.sol
- 2022-08-olympus\src\scripts\Deploy.sol
- 2022-08-olympus\src\test\Kernel.t.sol
- 2022-08-olympus\src\test\lib\ModuleTestFixtureGenerator.sol
- 2022-08-olympus\src\test\lib\UserFactory.sol
- 2022-08-olympus\src\test\lib\bonds\BondAggregator.sol
- 2022-08-olympus\src\test\lib\bonds\BondFixedTermCDA.sol
- 2022-08-olympus\src\test\lib\bonds\BondFixedTermTeller.sol
- 2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol
- 2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol
- 2022-08-olympus\src\test\lib\bonds\interfaces\IBondAggregator.sol
- 2022-08-olympus\src\test\lib\bonds\interfaces\IBondAuctioneer.sol
- 2022-08-olympus\src\test\lib\bonds\interfaces\IBondCDA.sol
- 2022-08-olympus\src\test\lib\bonds\interfaces\IBondCallback.sol
- 2022-08-olympus\src\test\lib\bonds\interfaces\IBondFixedTermTeller.sol
- 2022-08-olympus\src\test\lib\bonds\interfaces\IBondTeller.sol
- 2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol
- 2022-08-olympus\src\test\lib\larping.sol
- 2022-08-olympus\src\test\lib\quabi\Quabi.sol
- 2022-08-olympus\src\test\mocks\Faucet.sol
- 2022-08-olympus\src\test\mocks\KernelTestMocks.sol
- 2022-08-olympus\src\test\mocks\MockInvalidModule.sol
- 2022-08-olympus\src\test\mocks\MockModuleWriter.sol
- 2022-08-olympus\src\test\mocks\MockPrice.sol
- 2022-08-olympus\src\test\mocks\MockPriceFeed.sol
- 2022-08-olympus\src\test\mocks\MockValidModule.sol
- 2022-08-olympus\src\test\mocks\MockValidUpgradedModule.sol
- 2022-08-olympus\src\test\modules\INSTR.t.sol
- 2022-08-olympus\src\test\modules\MINTR.t.sol
- 2022-08-olympus\src\test\modules\PRICE.t.sol
- 2022-08-olympus\src\test\modules\RANGE.t.sol
- 2022-08-olympus\src\test\modules\TRSRY.t.sol
- 2022-08-olympus\src\test\modules\VOTES.t.sol
- 2022-08-olympus\src\test\policies\BondCallback.t.sol
- 2022-08-olympus\src\test\policies\Governance.t.sol
- 2022-08-olympus\src\test\policies\Heart.t.sol
- 2022-08-olympus\src\test\policies\Operator.t.sol
- 2022-08-olympus\src\test\policies\PriceConfig.t.sol
- 2022-08-olympus\src\test\policies\TreasuryCustodian.t.sol
- 2022-08-olympus\src\test\policies\VoterRegistration.t.sol
- 2022-08-olympus\src\utils\KernelUtils.sol

## Issues found

### Don't Initialize Variables with Default Value

#### Impact

Issue Information: [G001](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:

```
2022-08-olympus\src\Kernel.sol::397 => for (uint256 i = 0; i < reqLength; ) {
2022-08-olympus\src\scripts\Deploy.sol::239 => for (uint i = 0; i < 90; i++) {
2022-08-olympus\src\test\lib\UserFactory.sol::25 => for (uint256 i = 0; i < userNum; i++) {
2022-08-olympus\src\utils\KernelUtils.sol::43 => for (uint256 i = 0; i < 5; ) {
2022-08-olympus\src\utils\KernelUtils.sol::58 => for (uint256 i = 0; i < 32; ) {
```

### Cache Array Length Outside of Loop

#### Impact

Issue Information: [G002](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:

```
2022-08-olympus\src\Kernel.sol::300 => getPolicyIndex[policy_] = activePolicies.length - 1;
2022-08-olympus\src\Kernel.sol::304 => uint256 depLength = dependencies.length;
2022-08-olympus\src\Kernel.sol::310 => getDependentIndex[keycode][policy_] = moduleDependents[keycode].length - 1;
2022-08-olympus\src\Kernel.sol::334 => Policy lastPolicy = activePolicies[activePolicies.length - 1];
2022-08-olympus\src\Kernel.sol::352 => uint256 keycodeLen = allKeycodes.length;
2022-08-olympus\src\Kernel.sol::361 => uint256 policiesLen = activePolicies.length;
2022-08-olympus\src\Kernel.sol::380 => uint256 depLength = dependents.length;
2022-08-olympus\src\Kernel.sol::396 => uint256 reqLength = requests_.length;
2022-08-olympus\src\Kernel.sol::411 => uint256 depcLength = dependencies.length;
2022-08-olympus\src\Kernel.sol::418 => Policy lastPolicy = dependents[dependents.length - 1];
2022-08-olympus\src\external\OlympusERC20.sol::105 => revert("ECDSA: invalid signature length");
2022-08-olympus\src\external\OlympusERC20.sol::138 => // Check the signature length
2022-08-olympus\src\external\OlympusERC20.sol::141 => if (signature.length == 65) {
2022-08-olympus\src\external\OlympusERC20.sol::153 => } else if (signature.length == 64) {
2022-08-olympus\src\external\OlympusERC20.sol::285 => // 32 is the length in bytes of hash,
2022-08-olympus\src\interfaces\IBondAggregator.sol::65 => /// @dev                Should be used if length exceeds max to query entire array
2022-08-olympus\src\interfaces\IBondAuctioneer.sol::36 => /// @dev                    8. Is fixed term ? Vesting length (seconds) : Vesting expiration (timestamp).
2022-08-olympus\src\libraries\TransferHelper.sol::20 => require(success && (data.length == 0 || abi.decode(data, (bool))), "TRANSFER_FROM_FAILED");
2022-08-olympus\src\libraries\TransferHelper.sol::32 => require(success && (data.length == 0 || abi.decode(data, (bool))), "TRANSFER_FAILED");
2022-08-olympus\src\libraries\TransferHelper.sol::44 => require(success && (data.length == 0 || abi.decode(data, (bool))), "APPROVE_FAILED");
2022-08-olympus\src\modules\INSTR.sol::43 => uint256 length = instructions_.length;
2022-08-olympus\src\modules\INSTR.sol::48 => if (length == 0) revert INSTR_InstructionsCannotBeEmpty();
2022-08-olympus\src\modules\INSTR.sol::50 => for (uint256 i; i < length; ) {
2022-08-olympus\src\modules\INSTR.sol::61 => } else if (instruction.action == Actions.ChangeExecutor && i != length - 1) {
2022-08-olympus\src\modules\PRICE.sol::201 => /// @param  startObservations_ - Array of observations to initialize the moving average with. Must be of length numObservations.
2022-08-olympus\src\modules\PRICE.sol::212 => uint256 numObs = observations.length;
2022-08-olympus\src\modules\PRICE.sol::215 => if (startObservations_.length != numObs || lastObservationTime_ > uint48(block.timestamp))
2022-08-olympus\src\policies\BondCallback.sol::155 => uint256 len = tokens_.length;
2022-08-olympus\src\policies\Governance.sol::188 => if (instructions.length == 0) {
2022-08-olympus\src\policies\Governance.sol::278 => for (uint256 step; step < instructions.length; ) {
2022-08-olympus\src\policies\PriceConfig.sol::41 => /// @param startObservations_   Array of observations to initialize the moving average with. Must be of length numObservations.
2022-08-olympus\src\policies\TreasuryCustodian.sol::58 => uint256 len = tokens_.length;
2022-08-olympus\src\test\lib\ModuleTestFixtureGenerator.sol::18 => uint256 len = requests_.length;
2022-08-olympus\src\test\lib\ModuleTestFixtureGenerator.sol::36 => uint256 len = _requests.length;
2022-08-olympus\src\test\lib\ModuleTestFixtureGenerator.sol::63 => uint256 num = selectors.length;
2022-08-olympus\src\test\lib\bonds\BondAggregator.sol::156 => uint256 len = mkts.length;
2022-08-olympus\src\test\lib\bonds\BondAggregator.sol::182 => uint256 len = forPayout.length;
2022-08-olympus\src\test\lib\bonds\BondAggregator.sol::213 => uint256 len = ids.length;
2022-08-olympus\src\test\lib\bonds\BondFixedTermTeller.sol::160 => uint256 len = tokenIds_.length;
2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol::199 => length: secondsToConclusion,
2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol::216 => // Initial target debt is equal to capacity scaled by the ratio of the debt decay interval and the length of the market.
2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol::543 => uint256(meta.length) - timeRemaining,
2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol::544 => uint256(meta.length)
2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol::564 => // Calculate target debt from the timeNeutralCapacity and the ratio of debt decay interval and the length of the market
2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol::567 => uint256(meta.length)
2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol::101 => uint256 len = tokens_.length;
2022-08-olympus\src\test\lib\bonds\interfaces\IBondAggregator.sol::65 => /// @dev                Should be used if length exceeds max to query entire array
2022-08-olympus\src\test\lib\bonds\interfaces\IBondAuctioneer.sol::36 => /// @dev                    8. Is fixed term ? Vesting length (seconds) : Vesting expiration (timestamp).
2022-08-olympus\src\test\lib\bonds\interfaces\IBondCDA.sol::28 => uint48 vesting; // length of time from deposit to maturity if fixed-term, vesting timestamp if fixed-expiry
2022-08-olympus\src\test\lib\bonds\interfaces\IBondCDA.sol::37 => uint32 length; // time from creation to conclusion.
2022-08-olympus\src\test\lib\bonds\interfaces\IBondCDA.sol::75 => // l = length of program
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::65 => to.code.length == 0
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::80 => uint256 idsLength = ids.length; // Saves MLOADs.
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::82 => require(idsLength == amounts.length, "LENGTH_MISMATCH");
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::97 => // An array can't have a total length
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::107 => to.code.length == 0
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::126 => uint256 ownersLength = owners.length; // Saves MLOADs.
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::128 => require(ownersLength == ids.length, "LENGTH_MISMATCH");
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::167 => to.code.length == 0
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::186 => uint256 idsLength = ids.length; // Saves MLOADs.
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::188 => require(idsLength == amounts.length, "LENGTH_MISMATCH");
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::193 => // An array can't have a total length
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::203 => to.code.length == 0
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::221 => uint256 idsLength = ids.length; // Saves MLOADs.
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::223 => require(idsLength == amounts.length, "LENGTH_MISMATCH");
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::228 => // An array can't have a total length
2022-08-olympus\src\test\lib\quabi\Quabi.sol::35 => uint256 len = response.length;
2022-08-olympus\src\test\mocks\MockModuleWriter.sol::15 => uint256 len = requests_.length;
2022-08-olympus\src\test\mocks\MockModuleWriter.sol::30 => uint256 len = _requests.length;
2022-08-olympus\src\test\mocks\MockModuleWriter.sol::42 => if (output.length == 0) revert();
```

### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact

Issue Information: [G003](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:

```
2022-08-olympus\src\external\OlympusERC20.sol::245 => if (uint256(s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) {
2022-08-olympus\src\external\OlympusERC20.sol::611 => require(b > 0, errorMessage);
2022-08-olympus\src\libraries\FullMath.sol::35 => require(denominator > 0);
2022-08-olympus\src\libraries\FullMath.sol::122 => if (mulmod(a, b, denominator) > 0) {
2022-08-olympus\src\policies\Governance.sol::247 => if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
2022-08-olympus\src\test\modules\TRSRY.t.sol::98 => vm.assume(amount_ > 0);
2022-08-olympus\src\test\modules\TRSRY.t.sol::108 => vm.assume(amount_ > 0);
2022-08-olympus\src\test\modules\TRSRY.t.sol::126 => vm.assume(amount_ > 0);
2022-08-olympus\src\test\modules\TRSRY.t.sol::143 => vm.assume(amount_ > 0);
2022-08-olympus\src\utils\KernelUtils.sol::46 => if (char < 0x41 || char > 0x5A) revert InvalidKeycode(keycode_); // A-Z only
2022-08-olympus\src\utils\KernelUtils.sol::60 => if ((char < 0x61 || char > 0x7A) && char != 0x5f && char != 0x00) {
```

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact

Issue Information: [G006](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:

```
2022-08-olympus\src\external\OlympusERC20.sol::287 => return keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", hash));
2022-08-olympus\src\external\OlympusERC20.sol::304 => return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
2022-08-olympus\src\external\OlympusERC20.sol::315 => * they need in their contracts using a combination of `abi.encode` and `keccak256`.
2022-08-olympus\src\external\OlympusERC20.sol::360 => bytes32 hashedName = keccak256(bytes(name));
2022-08-olympus\src\external\OlympusERC20.sol::361 => bytes32 hashedVersion = keccak256(bytes(version));
2022-08-olympus\src\external\OlympusERC20.sol::362 => bytes32 typeHash = keccak256(
2022-08-olympus\src\external\OlympusERC20.sol::398 => return keccak256(abi.encode(typeHash, nameHash, versionHash, chainID, address(this)));
2022-08-olympus\src\external\OlympusERC20.sol::408 => * bytes32 digest = _hashTypedDataV4(keccak256(abi.encode(
2022-08-olympus\src\external\OlympusERC20.sol::409 => *     keccak256("Mail(address to,string contents)"),
2022-08-olympus\src\external\OlympusERC20.sol::411 => *     keccak256(bytes(mailContents))
2022-08-olympus\src\external\OlympusERC20.sol::665 => bytes32 private constant ERC20TOKEN_ERC1820_INTERFACE_ID = keccak256("ERC20Token");
2022-08-olympus\src\external\OlympusERC20.sol::835 => keccak256(
2022-08-olympus\src\external\OlympusERC20.sol::860 => bytes32 structHash = keccak256(
2022-08-olympus\src\test\lib\UserFactory.sol::9 => address(bytes20(uint160(uint256(keccak256("hevm cheat code")))));
2022-08-olympus\src\test\lib\UserFactory.sol::13 => bytes32 internal nextUser = keccak256(abi.encodePacked("users"));
2022-08-olympus\src\test\lib\UserFactory.sol::18 => nextUser = keccak256(abi.encodePacked(nextUser));
2022-08-olympus\src\test\lib\bonds\BondFixedTermTeller.sol::231 => keccak256(abi.encodePacked(underlying_, expiry_ / uint48(1 days)))
2022-08-olympus\src\test\lib\larping.sol::9 => address(bytes20(uint160(uint256(keccak256("hevm cheat code")))));
2022-08-olympus\src\test\lib\quabi\Quabi.sol::8 => Vm internal constant vm = Vm(address(bytes20(uint160(uint256(keccak256("hevm cheat code"))))));
2022-08-olympus\src\test\modules\PRICE.t.sol::101 => change = int256(uint256(keccak256(abi.encodePacked(nonce, i)))) % int256(1000);
2022-08-olympus\src\test\modules\PRICE.t.sol::128 => change = int256(uint256(keccak256(abi.encodePacked(nonce, i)))) % int256(1000);
2022-08-olympus\src\test\policies\PriceConfig.t.sol::124 => change = int256(uint256(keccak256(abi.encodePacked(nonce, i)))) % int256(1000);
```

### Long Revert Strings

#### Impact

Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:

```
2022-08-olympus\src\external\OlympusERC20.sol::107 => revert("ECDSA: invalid signature 's' value");
2022-08-olympus\src\external\OlympusERC20.sol::109 => revert("ECDSA: invalid signature 'v' value");
2022-08-olympus\src\external\OlympusERC20.sol::363 => "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
2022-08-olympus\src\external\OlympusERC20.sol::597 => require(c / a == b, "SafeMath: multiplication overflow");
2022-08-olympus\src\external\OlympusERC20.sol::738 => _allowances[sender][msg.sender].sub(amount, "ERC20: transfer amount exceeds allowance")
2022-08-olympus\src\external\OlympusERC20.sol::758 => "ERC20: decreased allowance below zero"
2022-08-olympus\src\external\OlympusERC20.sol::769 => require(sender != address(0), "ERC20: transfer from the zero address");
2022-08-olympus\src\external\OlympusERC20.sol::770 => require(recipient != address(0), "ERC20: transfer to the zero address");
2022-08-olympus\src\external\OlympusERC20.sol::774 => _balances[sender] = _balances[sender].sub(amount, "ERC20: transfer amount exceeds balance");
2022-08-olympus\src\external\OlympusERC20.sol::788 => require(account != address(0), "ERC20: burn from the zero address");
2022-08-olympus\src\external\OlympusERC20.sol::792 => _balances[account] = _balances[account].sub(amount, "ERC20: burn amount exceeds balance");
2022-08-olympus\src\external\OlympusERC20.sol::802 => require(owner != address(0), "ERC20: approve from the zero address");
2022-08-olympus\src\external\OlympusERC20.sol::803 => require(spender != address(0), "ERC20: approve to the zero address");
2022-08-olympus\src\external\OlympusERC20.sol::836 => "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
2022-08-olympus\src\external\OlympusERC20.sol::925 => "ERC20: burn amount exceeds allowance"
2022-08-olympus\src\modules\PRICE.sol::4 => import {AggregatorV2V3Interface} from "interfaces/AggregatorV2V3Interface.sol";
2022-08-olympus\src\modules\TRSRY.sol::5 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\modules\VOTES.sol::18 => ERC20("OlympusDAO Dummy Voting Tokens", "VOTES", 0)
2022-08-olympus\src\policies\BondCallback.sol::5 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\policies\Heart.sol::4 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\policies\Heart.sol::7 => import {IOperator} from "policies/interfaces/IOperator.sol";
2022-08-olympus\src\policies\Operator.sol::4 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\policies\Operator.sol::7 => import {IOperator} from "policies/interfaces/IOperator.sol";
2022-08-olympus\src\scripts\Deploy.sol::4 => import {AggregatorV2V3Interface} from "interfaces/AggregatorV2V3Interface.sol";
2022-08-olympus\src\scripts\Deploy.sol::271 => console2.log("RESERVE-ETH Price Feed deployed to:", address(reserveEthPriceFeed));
2022-08-olympus\src\test\Kernel.t.sol::77 => err = abi.encodeWithSignature("InvalidKeycode(bytes5)", Keycode.wrap("inval"));
2022-08-olympus\src\test\Kernel.t.sol::81 => err = abi.encodeWithSignature("InvalidKeycode(bytes5)", Keycode.wrap(""));
2022-08-olympus\src\test\Kernel.t.sol::89 => err = abi.encodeWithSignature("InvalidRole(bytes32)", Role.wrap("INVALID_ID"));
2022-08-olympus\src\test\Kernel.t.sol::150 => err = abi.encodeWithSignature("InvalidKeycode(bytes5)", Keycode.wrap("badkc"));
2022-08-olympus\src\test\Kernel.t.sol::156 => "Kernel_ModuleAlreadyInstalled(bytes5)",
2022-08-olympus\src\test\Kernel.t.sol::169 => err = abi.encodeWithSignature("Policy_ModuleDoesNotExist(bytes5)", testKeycode);
2022-08-olympus\src\test\Kernel.t.sol::187 => err = abi.encodeWithSignature("Kernel_PolicyAlreadyActivated(address)", address(policy));
2022-08-olympus\src\test\Kernel.t.sol::246 => err = abi.encodeWithSignature("Kernel_PolicyAlreadyActivated(address)", address(policy));
2022-08-olympus\src\test\Kernel.t.sol::254 => err = abi.encodeWithSignature("Module_PolicyNotPermitted(address)", address(policy));
2022-08-olympus\src\test\Kernel.t.sol::275 => err = abi.encodeWithSignature("Kernel_InvalidModuleUpgrade(bytes5)", Keycode.wrap("MOCKY"));
2022-08-olympus\src\test\Kernel.t.sol::281 => err = abi.encodeWithSignature("Kernel_InvalidModuleUpgrade(bytes5)", Keycode.wrap("MOCKY"));
2022-08-olympus\src\test\Kernel.t.sol::359 => err = abi.encodeWithSignature("Policy_OnlyRole(bytes32)", Role.wrap("tester"));
2022-08-olympus\src\test\lib\bonds\BondFixedTermTeller.sol::7 => import {IBondFixedTermTeller} from "./interfaces/IBondFixedTermTeller.sol";
2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol::5 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol::11 => import {IBondAggregator} from "../interfaces/IBondAggregator.sol";
2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol::5 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol::10 => import {IBondAggregator} from "../interfaces/IBondAggregator.sol";
2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol::11 => import {IBondAuctioneer} from "../interfaces/IBondAuctioneer.sol";
2022-08-olympus\src\test\lib\bonds\interfaces\IBondCDA.sol::5 => import {IBondAuctioneer} from "../interfaces/IBondAuctioneer.sol";
2022-08-olympus\src\test\lib\quabi\Quabi.sol::17 => inputs[2] = string(bytes.concat("./src/test/lib/quabi/jq.sh ", bytes(query), " ", bytes(path), ""));
2022-08-olympus\src\test\lib\quabi\Quabi.sol::27 => inputs[2] = string(bytes.concat("./src/test/lib/quabi/path.sh ", bytes(contractName), ".json", ""));
2022-08-olympus\src\test\lib\quabi\Quabi.sol::48 => string memory query = "'[.ast.nodes[-1].nodes[] | if .nodeType == \"FunctionDefinition\" and .kind == \"function\" then .functionSelector else empty end ]'";
2022-08-olympus\src\test\lib\quabi\Quabi.sol::55 => string memory query = string(bytes.concat("'[.ast.nodes[-1].nodes[] | if .nodeType == \"FunctionDefinition\" and .kind == \"function\" and ([.modifiers[] | .modifierName.name == \"", bytes(modifierName), "\" ] | any ) then .functionSelector else empty end ]'"));
2022-08-olympus\src\test\mocks\Faucet.sol::6 => import {ReentrancyGuard} from "solmate/utils/ReentrancyGuard.sol";
2022-08-olympus\src\test\mocks\MockPrice.sol::4 => import {MockERC20, ERC20} from "solmate/test/utils/mocks/MockERC20.sol";
2022-08-olympus\src\test\mocks\MockPriceFeed.sol::4 => import {AggregatorV2V3Interface} from "interfaces/AggregatorV2V3Interface.sol";
2022-08-olympus\src\test\modules\INSTR.t.sol::7 => import {ModuleTestFixtureGenerator} from "test/lib/ModuleTestFixtureGenerator.sol";
2022-08-olympus\src\test\modules\INSTR.t.sol::15 => import {MockValidUpgradedModule} from "test/mocks/MockValidUpgradedModule.sol";
2022-08-olympus\src\test\modules\MINTR.t.sol::10 => import {ModuleTestFixtureGenerator} from "test/lib/ModuleTestFixtureGenerator.sol";
2022-08-olympus\src\test\modules\PRICE.t.sol::7 => import {ModuleTestFixtureGenerator} from "test/lib/ModuleTestFixtureGenerator.sol";
2022-08-olympus\src\test\modules\PRICE.t.sol::9 => import {MockERC20, ERC20} from "solmate/test/utils/mocks/MockERC20.sol";
2022-08-olympus\src\test\modules\RANGE.t.sol::7 => import {ModuleTestFixtureGenerator} from "test/lib/ModuleTestFixtureGenerator.sol";
2022-08-olympus\src\test\modules\RANGE.t.sol::9 => import {MockERC20, ERC20} from "solmate/test/utils/mocks/MockERC20.sol";
2022-08-olympus\src\test\modules\TRSRY.t.sol::7 => import {ModuleTestFixtureGenerator} from "test/lib/ModuleTestFixtureGenerator.sol";
2022-08-olympus\src\test\modules\TRSRY.t.sol::9 => import {MockERC20} from "solmate/test/utils/mocks/MockERC20.sol";
2022-08-olympus\src\test\modules\VOTES.t.sol::8 => import {ModuleTestFixtureGenerator} from "test/lib/ModuleTestFixtureGenerator.sol";
2022-08-olympus\src\test\policies\BondCallback.t.sol::8 => import {BondFixedTermCDA} from "test/lib/bonds/BondFixedTermCDA.sol";
2022-08-olympus\src\test\policies\BondCallback.t.sol::9 => import {BondAggregator} from "test/lib/bonds/BondAggregator.sol";
2022-08-olympus\src\test\policies\BondCallback.t.sol::10 => import {BondFixedTermTeller} from "test/lib/bonds/BondFixedTermTeller.sol";
2022-08-olympus\src\test\policies\BondCallback.t.sol::11 => import {IBondAuctioneer as LibAuctioneer} from "test/lib/bonds/interfaces/IBondAuctioneer.sol";
2022-08-olympus\src\test\policies\BondCallback.t.sol::12 => import {RolesAuthority, Authority as SolmateAuthority} from "solmate/auth/authorities/RolesAuthority.sol";
2022-08-olympus\src\test\policies\BondCallback.t.sol::14 => import {MockERC20, ERC20} from "solmate/test/utils/mocks/MockERC20.sol";
2022-08-olympus\src\test\policies\BondCallback.t.sol::375 => "Callback_MarketNotSupported(uint256)",
2022-08-olympus\src\test\policies\BondCallback.t.sol::431 => "Callback_MarketNotSupported(uint256)",
2022-08-olympus\src\test\policies\Governance.t.sol::7 => import {ModuleTestFixtureGenerator} from "../lib/ModuleTestFixtureGenerator.sol";
2022-08-olympus\src\test\policies\Governance.t.sol::118 => governance.submitProposal(instructions_, "proposalName", "This is the proposal URI");
2022-08-olympus\src\test\policies\Governance.t.sol::128 => governance.submitProposal(instructions_, "proposalName", "This is the proposal URI");
2022-08-olympus\src\test\policies\Governance.t.sol::139 => governance.submitProposal(instructions_, "proposalName", "This is the proposal URI");
2022-08-olympus\src\test\policies\Governance.t.sol::150 => governance.submitProposal(instructions_, "proposalName", "This is the proposal URI");
2022-08-olympus\src\test\policies\Heart.t.sol::8 => import {MockERC20, ERC20} from "solmate/test/utils/mocks/MockERC20.sol";
2022-08-olympus\src\test\policies\Heart.t.sol::17 => import {IOperator, ERC20, IBondAuctioneer, IBondCallback} from "policies/interfaces/IOperator.sol";
2022-08-olympus\src\test\policies\Operator.t.sol::8 => import {BondFixedTermCDA} from "test/lib/bonds/BondFixedTermCDA.sol";
2022-08-olympus\src\test\policies\Operator.t.sol::9 => import {BondAggregator} from "test/lib/bonds/BondAggregator.sol";
2022-08-olympus\src\test\policies\Operator.t.sol::10 => import {BondFixedTermTeller} from "test/lib/bonds/BondFixedTermTeller.sol";
2022-08-olympus\src\test\policies\Operator.t.sol::11 => import {RolesAuthority, Authority as SolmateAuthority} from "solmate/auth/authorities/RolesAuthority.sol";
2022-08-olympus\src\test\policies\Operator.t.sol::13 => import {MockERC20, ERC20} from "solmate/test/utils/mocks/MockERC20.sol";
2022-08-olympus\src\test\policies\Operator.t.sol::409 => "Operator_AmountLessThanMinimum(uint256,uint256)",
2022-08-olympus\src\test\policies\Operator.t.sol::424 => "Operator_AmountLessThanMinimum(uint256,uint256)",
2022-08-olympus\src\test\policies\PriceConfig.t.sol::8 => import {MockERC20, ERC20} from "solmate/test/utils/mocks/MockERC20.sol";
2022-08-olympus\src\test\policies\TreasuryCustodian.t.sol::7 => import {MockERC20} from "solmate/test/utils/mocks/MockERC20.sol";
2022-08-olympus\src\test\policies\TreasuryCustodian.t.sol::13 => import {TreasuryCustodian} from "src/policies/TreasuryCustodian.sol";
```

### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact

Issue Information: [G008](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:

```
2022-08-olympus\src\external\OlympusERC20.sol::243 => // vice versa. If your library also generates signatures with 0/1 for v instead 27/28, add 27 to v to accept
2022-08-olympus\src\external\OlympusERC20.sol::520 => * https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
2022-08-olympus\src\external\OlympusERC20.sol::641 => // this feature: see https://github.com/ethereum/solidity/issues/4637
2022-08-olympus\src\interfaces\IBondAuctioneer.sol::41 => /// @dev                        Should be calculated as: (payoutDecimals - quoteDecimals) - ((payoutPriceDecimals - quotePriceDecimals) / 2)
2022-08-olympus\src\libraries\FullMath.sol::13 => /// @dev Credit to Remco Bloemen under MIT license https://xn--2-umb.com/21/muldiv
2022-08-olympus\src\libraries\FullMath.sol::21 => // Compute the product mod 2**256 and mod 2**256 - 1
2022-08-olympus\src\libraries\FullMath.sol::24 => // variables such that product = prod1 * 2**256 + prod0
2022-08-olympus\src\libraries\FullMath.sol::42 => // Make sure the result is less than 2**256.
2022-08-olympus\src\libraries\FullMath.sol::76 => // to flip `twos` such that it is 2**256 / twos.
2022-08-olympus\src\libraries\FullMath.sol::83 => // Invert denominator mod 2**256
2022-08-olympus\src\libraries\FullMath.sol::85 => // modulo 2**256 such that denominator * inv = 1 mod 2**256.
2022-08-olympus\src\libraries\FullMath.sol::87 => // correct for four bits. That is, denominator * inv = 1 mod 2**4
2022-08-olympus\src\libraries\FullMath.sol::92 => inv *= 2 - denominator * inv; // inverse mod 2**8
2022-08-olympus\src\libraries\FullMath.sol::97 => inv *= 2 - denominator * inv; // inverse mod 2**256
2022-08-olympus\src\libraries\FullMath.sol::101 => // correct result modulo 2**256. Since the precoditions guarantee
2022-08-olympus\src\libraries\FullMath.sol::102 => // that the outcome is less than 2**256, this is the final result.
2022-08-olympus\src\policies\Operator.sol::372 => int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);
2022-08-olympus\src\policies\Operator.sol::419 => uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
2022-08-olympus\src\policies\Operator.sol::420 => uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;
2022-08-olympus\src\policies\Operator.sol::427 => int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);
2022-08-olympus\src\policies\Operator.sol::786 => ) * (FACTOR_SCALE + RANGE.spread(true) * 2)) /
2022-08-olympus\src\scripts\Deploy.sol::144 => uint32(7) // regenObserve    // 21
2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol::157 => // scaleAdjustment should be equal to (payoutDecimals - quoteDecimals) - ((payoutPriceDecimals - quotePriceDecimals) / 2)
2022-08-olympus\src\test\lib\bonds\bases\BondBaseCDA.sol::520 => // 2. If a tune interval has passed since last tune adjustment and the market is undersold
2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol::134 => // 2. Calculate protocol fee as the total expected fee amount minus the referrer fee
2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol::251 => int256 num1 = __days + 68569 + 2440588; // 2440588 = OFFSET19700101
2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol::253 => num1 = num1 - (146097 * num2 + 3) / 4;
2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol::255 => num1 = num1 - (1461 * _year) / 4 + 31;
2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol::256 => int256 _month = (80 * num1) / 2447;
2022-08-olympus\src\test\lib\bonds\bases\BondBaseTeller.sol::257 => int256 _day = num1 - (2447 * _month) / 80;
2022-08-olympus\src\test\lib\bonds\interfaces\IBondAuctioneer.sol::41 => /// @dev                        Should be calculated as: (payoutDecimals - quoteDecimals) - ((payoutPriceDecimals - quotePriceDecimals) / 2)
2022-08-olympus\src\test\lib\larping.sol::13 => // ,address
2022-08-olympus\src\test\lib\larping.sol::38 => // ,bool
2022-08-olympus\src\test\lib\larping.sol::63 => // ,bytes32
2022-08-olympus\src\test\lib\larping.sol::88 => // ,string
2022-08-olympus\src\test\lib\larping.sol::113 => // ,uint256
2022-08-olympus\src\test\lib\larping.sol::138 => // ,uint8
2022-08-olympus\src\test\modules\PRICE.t.sol::34 => vm.warp(51 * 365 * 24 * 60 * 60); // Set timestamp at roughly Jan 1, 2021 (51 years since Unix epoch)
2022-08-olympus\src\test\modules\RANGE.t.sol::36 => vm.warp(51 * 365 * 24 * 60 * 60); // Set timestamp at roughly Jan 1, 2021 (51 years since Unix epoch)
2022-08-olympus\src\test\modules\TRSRY.t.sol::174 => TRSRY.setDebt(ngmi, debtor, INITIAL_TOKEN_AMOUNT / 2);
2022-08-olympus\src\test\modules\TRSRY.t.sol::176 => assertEq(TRSRY.reserveDebt(ngmi, debtor), INITIAL_TOKEN_AMOUNT / 2);
2022-08-olympus\src\test\modules\TRSRY.t.sol::177 => assertEq(TRSRY.totalDebt(ngmi), INITIAL_TOKEN_AMOUNT / 2);
2022-08-olympus\src\test\modules\TRSRY.t.sol::191 => TRSRY.setDebt(ngmi, debtor, INITIAL_TOKEN_AMOUNT / 2);
2022-08-olympus\src\test\policies\BondCallback.t.sol::81 => vm.warp(51 * 365 * 24 * 60 * 60); // Set timestamp at roughly Jan 1, 2021 (51 years since Unix epoch)
2022-08-olympus\src\test\policies\BondCallback.t.sol::200 => ohm.mint(alice, testOhm * 20);
2022-08-olympus\src\test\policies\BondCallback.t.sol::201 => reserve.mint(alice, testReserve * 20);
2022-08-olympus\src\test\policies\BondCallback.t.sol::207 => ohm.approve(address(operator), testOhm * 20);
2022-08-olympus\src\test\policies\BondCallback.t.sol::209 => reserve.approve(address(operator), testReserve * 20);
2022-08-olympus\src\test\policies\BondCallback.t.sol::212 => ohm.approve(address(teller), testOhm * 20);
2022-08-olympus\src\test\policies\BondCallback.t.sol::214 => reserve.approve(address(teller), testReserve * 20);
2022-08-olympus\src\test\policies\BondCallback.t.sol::221 => // 2. Internal bond (OHM -> OHM)
2022-08-olympus\src\test\policies\BondCallback.t.sol::225 => // 4. Regular OHM bond that will not be whitelisted
2022-08-olympus\src\test\policies\BondCallback.t.sol::271 => uint256 minimumPrice = (priceSignificand / 2) *
2022-08-olympus\src\test\policies\Heart.t.sol::64 => vm.warp(51 * 365 * 24 * 60 * 60); // Set timestamp at roughly Jan 1, 2021 (51 years since Unix epoch)
2022-08-olympus\src\test\policies\Operator.t.sol::73 => vm.warp(51 * 365 * 24 * 60 * 60); // Set timestamp at roughly Jan 1, 2021 (51 years since Unix epoch)
2022-08-olympus\src\test\policies\Operator.t.sol::185 => ohm.mint(alice, testOhm * 20);
2022-08-olympus\src\test\policies\Operator.t.sol::186 => reserve.mint(alice, testReserve * 20);
2022-08-olympus\src\test\policies\Operator.t.sol::192 => ohm.approve(address(operator), testOhm * 20);
2022-08-olympus\src\test\policies\Operator.t.sol::194 => reserve.approve(address(operator), testReserve * 20);
2022-08-olympus\src\test\policies\Operator.t.sol::197 => ohm.approve(address(teller), testOhm * 20);
2022-08-olympus\src\test\policies\Operator.t.sol::199 => reserve.approve(address(teller), testReserve * 20);
2022-08-olympus\src\test\policies\Operator.t.sol::869 => uint256 amountIn = auctioneer.maxAmountAccepted(id, guardian) / 2;
2022-08-olympus\src\test\policies\Operator.t.sol::900 => uint256 amountIn = auctioneer.maxAmountAccepted(id, guardian) / 2;
2022-08-olympus\src\test\policies\Operator.t.sol::2044 => ) * (1e4 + range.spread(true) * 2)) / 1e4;
2022-08-olympus\src\test\policies\PriceConfig.t.sol::38 => vm.warp(51 * 365 * 24 * 60 * 60); // Set timestamp at roughly Jan 1, 2021 (51 years since Unix epoch)
```

### Unsafe ERC20 Operation(s)

#### Impact

Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:

```
2022-08-olympus\src\policies\Governance.sol::259 => VOTES.transferFrom(msg.sender, address(this), userVotes);
2022-08-olympus\src\policies\Governance.sol::312 => VOTES.transferFrom(address(this), msg.sender, userVotes);
2022-08-olympus\src\test\lib\bonds\BondFixedTermTeller.sol::115 => underlying_.transferFrom(msg.sender, address(this), amount_);
2022-08-olympus\src\test\modules\MINTR.t.sol::87 => ohm.approve(address(MINTR), amount_);
2022-08-olympus\src\test\modules\MINTR.t.sol::107 => ohm.approve(users[0], amount_);
2022-08-olympus\src\test\modules\TRSRY.t.sol::156 => ngmi.approve(address(TRSRY), amount_);
2022-08-olympus\src\test\modules\VOTES.t.sol::50 => VOTES.transfer(address(0), 10);
2022-08-olympus\src\test\modules\VOTES.t.sol::74 => VOTES.transferFrom(address(1), address(2), 3);
2022-08-olympus\src\test\policies\BondCallback.t.sol::207 => ohm.approve(address(operator), testOhm * 20);
2022-08-olympus\src\test\policies\BondCallback.t.sol::209 => reserve.approve(address(operator), testReserve * 20);
2022-08-olympus\src\test\policies\BondCallback.t.sol::212 => ohm.approve(address(teller), testOhm * 20);
2022-08-olympus\src\test\policies\BondCallback.t.sol::214 => reserve.approve(address(teller), testReserve * 20);
2022-08-olympus\src\test\policies\Governance.t.sol::96 => VOTES.approve(address(governance), type(uint256).max);
2022-08-olympus\src\test\policies\Governance.t.sol::98 => VOTES.approve(address(governance), type(uint256).max);
2022-08-olympus\src\test\policies\Governance.t.sol::100 => VOTES.approve(address(governance), type(uint256).max);
2022-08-olympus\src\test\policies\Governance.t.sol::102 => VOTES.approve(address(governance), type(uint256).max);
2022-08-olympus\src\test\policies\Governance.t.sol::104 => VOTES.approve(address(governance), type(uint256).max);
2022-08-olympus\src\test\policies\Operator.t.sol::192 => ohm.approve(address(operator), testOhm * 20);
2022-08-olympus\src\test\policies\Operator.t.sol::194 => reserve.approve(address(operator), testReserve * 20);
2022-08-olympus\src\test\policies\Operator.t.sol::197 => ohm.approve(address(teller), testOhm * 20);
2022-08-olympus\src\test\policies\Operator.t.sol::199 => reserve.approve(address(teller), testReserve * 20);
```

### Unspecific Compiler Version Pragma

#### Impact

Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:

```
2022-08-olympus\src\external\OlympusERC20.sol::2 => pragma solidity >=0.7.5;
2022-08-olympus\src\interfaces\AggregatorV2V3Interface.sol::2 => pragma solidity ^0.8.0;
2022-08-olympus\src\interfaces\IBondAggregator.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\interfaces\IBondAuctioneer.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\interfaces\IBondCallback.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\interfaces\IBondTeller.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\interfaces\IWETH9.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\libraries\FullMath.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\libraries\TransferHelper.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\policies\interfaces\IHeart.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\policies\interfaces\IOperator.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\lib\ModuleTestFixtureGenerator.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\lib\UserFactory.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\lib\bonds\interfaces\IBondAggregator.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\lib\bonds\interfaces\IBondAuctioneer.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\lib\bonds\interfaces\IBondCDA.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\lib\bonds\interfaces\IBondCallback.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\lib\bonds\interfaces\IBondFixedTermTeller.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\lib\bonds\interfaces\IBondTeller.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\lib\bonds\lib\ERC1155.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\lib\larping.sol::1 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\mocks\MockPriceFeed.sol::2 => pragma solidity ^0.8.0;
2022-08-olympus\src\test\modules\INSTR.t.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\modules\PRICE.t.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\modules\RANGE.t.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\modules\VOTES.t.sol::3 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\policies\BondCallback.t.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\policies\Governance.t.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\policies\Heart.t.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\policies\Operator.t.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\policies\PriceConfig.t.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\policies\TreasuryCustodian.t.sol::2 => pragma solidity >=0.8.0;
2022-08-olympus\src\test\policies\VoterRegistration.t.sol::2 => pragma solidity >=0.8.0;
```

### Do not use Deprecated Library Functions

#### Impact

Issue Information: [L005](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l005---do-not-use-deprecated-library-functions)

#### Findings:

```
2022-08-olympus\src\libraries\TransferHelper.sol::35 => function safeApprove(
2022-08-olympus\src\policies\BondCallback.sol::57 => ohm.safeApprove(address(MINTR), type(uint256).max);
2022-08-olympus\src\policies\Operator.sol::167 => ohm.safeApprove(address(MINTR), type(uint256).max);
```
