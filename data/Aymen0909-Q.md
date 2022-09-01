# QA Report

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Immutable addresses lack zero address `address(0)` checks | 6|
| 2      | Setters should check the input value and revert if it's the zero address or zero  |  2 |
| 3      | Event should be emitted in setters   |  6  |
| 4      | Named return variables not used anywhere in the function | 1 |
| 5      | Related data should be grouped in a struct |  8 |

## Findings

### 1- Missing zero address `address(0)` checks  :

Constructors should check the address written in an immutable address variable is not the zero address

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/Kernel.sol

[kernel = kernel_;](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L66)

File: src/modules/MINTR.sol

[ohm = OHM(ohm_);](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/MINTR.sol#L16)

File: src/modules/RANGE.sol

[ohm = tokens_[0];](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L102)

[reserve = tokens_[1];](https://github.com/code-423n4/2022-08-olympus/blob/main/src/modules/RANGE.sol#L103)

File: src/policies/Operator.sol

[ohm = tokens_[0];](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L121)

[reserve = tokens_[1];](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L123)

#### Mitigation
Add non-zero address checks in the constructors for the instances aforementioned.


### 2- Setters should check the input value and revert if it's the zero address or zero :

Setters should check the input values and revert if it's the zero address or zero.

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/Kernel.sol

[function changeKernel(Kernel newKernel_)](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L76)

File: src/policies/Heart.sol

[function setRewardTokenAndAmount(ERC20 token_, uint256 reward_)](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Heart.sol#L140)

#### Mitigation
Add non-zero checks - address or uint - to the setters aforementioned.

### 3- Event should be emitted in setters :

Setters should emit an event so that Dapps can detect important changes to storage.

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/Kernel.sol

[function changeKernel(Kernel newKernel_)](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L76)

[function setActiveStatus(bool activate_)](https://github.com/code-423n4/2022-08-olympus/blob/main/src/Kernel.sol#L126)

File: src/policies/BondCallback.sol

[function setOperator(Operator operator_)](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L190)

File: src/policies/Operator.sol

[function setBondContracts(IBondAuctioneer auctioneer_, IBondCallback callback_)](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/Operator.sol#L586)

File: src/policies/VoterRegistration.sol

[function issueVotesTo(address wallet_, uint256 amount_)](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L45)

[function revokeVotesFrom(address wallet_, uint256 amount_)](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/VoterRegistration.sol#L53)

#### Mitigation
Emit an event in the functions aforementioned.

### 4- Named return variables not used anywhere in the function :

Named return variable should be used inside the function or if not they should be removed to avoid confusion.

#### Impact - NON CRITICAL

#### Proof of Concept
Instances include:

File: src/policies/BondCallback.sol

[returns (uint256 in_, uint256 out_)](https://github.com/code-423n4/2022-08-olympus/blob/main/src/policies/BondCallback.sol#L177)

#### Mitigation

Either use the named return variables or remove them.

### 5- Related data should be grouped in a struct :

When there are mappings that use the same key value, having separate fields is error prone, for instance in case of deletion or with future new fields.

#### Impact - NON CRITICAL

#### Proof of Concept

Instances include:

```
File: src/policies/Governance.sol

96      mapping(uint256 => ProposalMetadata) public getProposalMetadata;
99      mapping(uint256 => uint256) public totalEndorsementsForProposal;
102     mapping(uint256 => mapping(address => uint256)) public userEndorsementsForProposal;
105     mapping(uint256 => bool) public proposalHasBeenActivated;
108     mapping(uint256 => uint256) public yesVotesForProposal;
111     mapping(uint256 => uint256) public noVotesForProposal;
114     mapping(uint256 => mapping(address => uint256)) public userVotesForProposal;
117     mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;
```

#### Mitigation

Group the related data in a struct and use one mapping:

```
struct Proposal {
        ProposalMetadata metadata;
        uint256 totalEndorsementsForProposal;
        uint256 yesVotesForProposal;
        uint256 noVotesForProposal;
        bool proposalHasBeenActivated;
        mapping(address => uint256) userEndorsementsForProposal;
        mapping(address => uint256) userVotesForProposal;
        mapping(address => bool) tokenClaimsForProposal;
    }
```

And it would be used as a state variable :

```
mapping(uint256 => Proposal) public proposals;
```