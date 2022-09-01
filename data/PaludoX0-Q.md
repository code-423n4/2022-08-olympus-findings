https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Heart.sol#L135
https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/policies/Operator.sol#L624
Using the toggle activate and deactivate could create confusion if there are more operator admin: in case of emergency two admins could call the function toggleActive at the same time and reactivate the operator or the heart
It’s preferable to implement two more explicit functions: activate() and deactivate()

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L180
Comment shall be read as  /// @dev    Keycode -> Policy -> Function Selector -> bool for permission

https://github.com/code-423n4/2022-08-olympus/blob/2a0b515012b4a40076f6eac487f7816aafb8724a/src/Kernel.sol#L235
function executeAction execute important actions, such change admin or executor. At least for these two actions a two-step process is preferable with owner proposing new address and transfer completed when new address execute a call accepting the role.
