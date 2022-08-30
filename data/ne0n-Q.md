In contract `Kernel` described in file ***"src/Kernel.sol"***, there exists a function `executeAction` which executes action like module install, upgrade, policy related actions etc. This function can also modify the ***executor*** and ***admin*** addresses. This function is protected by `onlyExecutor` modifier and **not** `onlyAdmin` modifier which could result in ***executor*** modifying ***admin*** address.

Line of Code:
[Function Definition] (https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L235)
[Modify the admin address] (https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L253)

Mitigation:
Allow only ***admin*** to change admin address.