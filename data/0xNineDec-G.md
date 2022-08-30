<h3>Gas Optimizations</h3> 

|       | Title                                                                                    | N° of Appearances |
| :---: | :--------------------------------------------------------------------------------------- | :---------------: |
| [G-1] | Constants that are <code>private</code> instead of <code>public</code> save gas.         |         8         |
| [G-2] | Variables set only while constructing should be <code>immutable</code>                   |         3         |
| [G-3] | Multiple mappings could be combined                                                      |         8         |
| [G-4] | Users can manipulate string chain size                                                   |         1         |
| [G-5] | Comparing a <code>uint</code> with <code>!=</code> is cheaper than using <code>></code>  |         1         |

<em>Total: 21 appearances over 5 issues.</em> 


 <h2>Gas Optimizations</h2> 
<h3> [G-1] Constants that are <code>private</code> instead of <code>public</code> save gas. </h3> 
Because constant variables should be declared an initialized as a one-liner, their value could be easily retrieved by reading directly the source code. Removing the <code>public</code> modifier and using <code>private</code> instead, does not create a function getter for the public constant saving around <code>3500 gas</code> on deployment.<br><br><em>Found 8 times</em>

```solidity
RANGE.sol   L65:       uint256 public constant FACTOR_SCALE = 1e4;
```
```solidity
Operator.sol   L89:       uint32 public constant FACTOR_SCALE = 1e4;
```
```solidity
Governance.sol   L121:       uint256 public constant SUBMISSION_REQUIREMENT = 100;
Governance.sol   L124:       uint256 public constant ACTIVATION_DEADLINE = 2 weeks;
Governance.sol   L127:       uint256 public constant GRACE_PERIOD = 1 weeks;
Governance.sol   L130:       uint256 public constant ENDORSEMENT_THRESHOLD = 20;
Governance.sol   L133:       uint256 public constant EXECUTION_THRESHOLD = 33;
Governance.sol   L137:       uint256 public constant EXECUTION_TIMELOCK = 3 days;
```
<br><h3> [G-2] Variables set only while constructing should be <code>immutable</code> </h3> 
This operation reduces the gas cost each time a variable is consulted and also avoids a Gsset (20,000 gas) while constructing. <br><br><em>Found 3 times</em>

```solidity
Heart.sol   L48:       IOperator internal _operator;
```
```solidity
BondCallback.sol   L43:       aggregator = aggregator_;
BondCallback.sol   L44:       ohm = ohm_;
```
<br><h3> [G-3] Multiple mappings could be combined </h3> 
If there are used mappings with repeated types of keys, it is a sign that they could be refactored into a single mapping that points to a struct when appropriate. Depending on the data types and their sizes, this could avoid triggering a Gsset operation (consumes 20,000 gas while changing from zero to a non zero value). Because memory slots are calculated via <code>keccak256</code>, reducing the amount of slots also reduce for the compiler the need to compute the keys' hash.<br><br><em>Found 8 times</em>

```solidity
Governance.sol   L96:       mapping(uint256 => ProposalMetadata) public getProposalMetadata;
Governance.sol   L99:       mapping(uint256 => uint256) public totalEndorsementsForProposal;
Governance.sol   L102:       mapping(uint256 => mapping(address => uint256)) public userEndorsementsForProposal;
Governance.sol   L105:       mapping(uint256 => bool) public proposalHasBeenActivated;
Governance.sol   L108:       mapping(uint256 => uint256) public yesVotesForProposal;
Governance.sol   L111:       mapping(uint256 => uint256) public noVotesForProposal;
Governance.sol   L114:       mapping(uint256 => mapping(address => uint256)) public userVotesForProposal;
Governance.sol   L117:       mapping(uint256 => mapping(address => bool)) public tokenClaimsForProposal;
```
<br><h3> [G-4] Users can manipulate string chain size </h3> 
Users can freely use string chains with uncapped size. When the <code>32 bytes</code> slot size is exceeded, a new slow will be used to render and store that string. In order to provide a better user experience, it is advisable to constraint the user-input string size.<br><br><em>Found 1 time</em>

```solidity
Governance.sol   L162:       string memory proposalURI_
```
<br><h3> [G-5] Comparing a <code>uint</code> with <code>!=</code> is cheaper than using <code>></code>  </h3> 
For unsigned integers, it is cheaper to check that they are non zero values with the unequal operator rather than checking their greatness. While compiling with the optimizer enabled this change saves around <code>6 gas</code>.<br><br><em>Found 1 time</em>

```solidity
Governance.sol   L247:       if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
```
<br>