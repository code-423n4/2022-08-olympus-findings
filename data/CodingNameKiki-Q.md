1. Override function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings

src/modules/VOTES.sol
45: function transfer(address to_, uint256 amount_) public pure override returns (bool) {
51: function transferFrom(

src/policies/Operator.sol
272: function swap(

src/policies/BondCallback.sol
83: function whitelist(address teller_, uint256 id_)
100: function callback(

2. Public functions not called by the contract should be declared external instead.

src/modules/TRSRY.sol
75: function withdrawReserves(

src/modules/MINTR.sol
33: function mintOhm(address to_, uint256 amount_) public permissioned {
37: function burnOhm(address from_, uint256 amount_) public permissioned {

src/modules/RANGE.sol
215: function updateMarket(

src/modules/VOTES.sol
51: function transferFrom(

src/modules/INSTR.sol
37: function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {

src/policies/Governance.sol
145: function getMetadata(uint256 proposalId_) public view returns (ProposalMetadata memory) {
151: function getActiveProposal() public view returns (ActivatedProposal memory) {
