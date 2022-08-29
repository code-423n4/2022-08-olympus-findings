Consider emitting an event when after tokens are safely reclaimed by the user. This allows for off chain monitoring in addition to allowing end users to observe and trust that these changes have occurred correctly.

This was observed on line `312` of `reclaimVotes` in the `Governance.sol` contract. 