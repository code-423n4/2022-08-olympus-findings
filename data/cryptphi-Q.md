1. **Missing zero address check**
The following are missing checks for zero address.

**Occurrences**
Kernel.executeAction() - is missing zero address validation, allowing the executor and/or admin address to be able to be set to address(0)
Kernel.grantRole() - Address(0) can be granted role due to no check for zero address input



2. Single step critical actionsThe executeAction() function carries out some critical actions which should be broken down into two step calls. e.g change of admin and change of executor


3. Missing event and emit
Policy.setActiveStatus()

4. Return values ignored
Kernel._reconfigurePolicies(Keycode) ignores return value by dependents[i].configureDependencies()


5. CEI pattern not followed
The following functions do not follow the Check-Effect-Interact pattern

**Occurrences**
Kernel._activatePolicy() - calls policy_.configureDependencies() before updating state variables
Kernel._pruneFromDependents() - calls policy_.configureDependencies() before updating state variables 
OlympusTreasury.repayLoan() in TRSRY.sol - external call token_.safeTransferFrom() before state variables reserveDebt and totalDebt updates.
Operator.initialize() - external calls before state variables updates.Operator.operate() - external calls before state variables updates
