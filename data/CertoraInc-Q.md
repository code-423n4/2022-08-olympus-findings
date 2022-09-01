# QA
* Forgot to remove some TODO comments:
  * TreasuryCustodian - lines 51, 52, 56
  * OlympusERC20 - line 664
  * Operator - line 657
  * Deploy - line 145
* Use constants for roles names instead of writing the raw string in every place it used. This will prevent possible typos and will make changing a role name easier (just change the constant value instead of changing every instance).
* The comment in [Kernel line 180](https://github.com/code-423n4/2022-08-olympus/blob/b5e139d732eb4c07102f149fb9426d356af617aa/src/Kernel.sol#L180) is wrong - it says the mapping maps a policy to a keycode, but the opposite is correct - it maps a keycode to a policy
    ```sol
    /// @dev    Policy -> Keycode -> Function Selector -> bool for permission
    mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions;
    ```
    The correct one:
    ```sol
    /// @dev    Keycode -> Policy -> Function Selector -> bool for permission
    mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions;
    ```
* There isn't any functionality to remove a module in the `Kernel` contract, which might be a thing you'd like to do.
* A user is not able to use his new votes if he get more votes after he already voted, but these votes will be counted as part of the total supply. In the worst case, this can lead to a proposal not being able to pass because the total supply will be increased and the users won't be able to use their votes in order to pass the execution threshold
* Pragmas should be locked to a specific compiler version, to avoid contracts getting deployed using a different version, which may have a greater risk of undiscovered bugs.
