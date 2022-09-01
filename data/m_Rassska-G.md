# Table of contents

- **[[0x0] Disclaimer](#0x0)**
- **[[G-01] Try ++i instead of i++](G-01)**
- **[[G-02] Consider `a = a + b` instead of `a += b`](G-02)**
- **[[G-03] Consider marking onlyOwner functions as payable](G-03)**
- **[[G-04] Use binary shifting instead of `a / 2^x, x > 0`](G-04)**
- **[[G-05] Cache state variables, `MLOAD` << `SLOAD`](G-05)**
- **[[G-06] Add `require()` before some computations, if it makes sense](G-06)**
- **[[G-07] `Internal` functions can be inlined](G-07)**
- **[[G-08] Use `private/internal` for `constants/immutable` variables instead of `public`](G-08)**
- **[[G-09] Use const values instead of `type(uint256).max`](G-09)**
- **[[G-10] Mark functions as `external` instead of `public`, if there are no internal calls](G-10)**
- **[[G-11] Use `calldataload` instead of `mload`](G-11)**
- **[[G-12] Define state variables as `immutable/const`](G-12)


## Disclaimer<a name="0x0"></a>

- Please, consider everything described below as a general recommendation. These notes will represent potential possibilities to optimize gas consumption. It's okay, if something is not suitable in your case. Just let me know the reason in the comments section. Enjoy!

## **[G-01] Try ++i instead of i++**<a name="G-01"></a>

### ***Description:***

  - In case of `i++`, the compiler has to to create a temp variable to return `i` (if there's a need) and then `i` gets incremented.  
  - In case of `++i`, the compiler just simply returns already incremented value.

### ***All occurances:***

  - Contracts:
  
    ```Solidity
      file: src/utils/KernelUtils.sol
      ...............................
      
        // Lines: [63-63]
          unchecked {
              i++;
          }

        // Lines: [58-60]
          unchecked {
              i++;
          }
    ```

## **[G-02] Consider `a = a + b` instead of `a += b`**<a name="G-02"></a>

### ***Description:***

- It has an impact on the deployment cost and the cost for distinct transaction as well.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/modules/PRICE.sol 
      ...............................
      
        // Lines: [136-136]
            _movingAverage += (currentPrice - earliestPrice) / numObs;

        // Lines: [138-138]
            _movingAverage -= (earliestPrice - currentPrice) / numObs; 

        // Lines: [222-222]
            total += startObservations_[i];

      file: src/modules/TRSRY.sol 
      ...............................

        // Lines: [96-97]
            reserveDebt[token_][msg.sender] += amount_;
            totalDebt[token_] += amount_;

        // Lines: [131-131]
            if (oldDebt < amount_) totalDebt[token_] += amount_ - oldDebt;

        // Lines: [115-116]
            reserveDebt[token_][msg.sender] -= received;
            totalDebt[token_] -= received;

        // Lines: [132-132]
            else totalDebt[token_] -= oldDebt - amount_;

      file: src/modules/VOTES.sol 
      ...............................

        // Lines: [58-58]
            balanceOf[to_] += amount_;

        // Lines: [56-56]
            balanceOf[from_] -= amount_;

      file: src/policies/BondCallback.sol 
      ...............................

        // Lines: [143-144]
            _amountsPerMarket[id_][0] += inputAmount_;
            _amountsPerMarket[id_][1] += outputAmount_;

      file: src/policies/Governance.sol 
      ...............................

        // Lines: [194-194]
            totalEndorsementsForProposal[proposalId_] -= previousEndorsement;

        // Lines: [198-198]
            totalEndorsementsForProposal[proposalId_] += userVotes;

        // Lines: [251-255]
            if (for_) {
                yesVotesForProposal[activeProposal.proposalId] += userVotes;
            } else {
                noVotesForProposal[activeProposal.proposalId] += userVotes;
            }

      file:  src/policies/Heart.sol
      ...............................

        // Lines: [103-103]
            lastBeat += frequency();

        // Lines: [138-138]
        // Lines: [138-138]

    ```

## **[G-03] Consider marking onlyOwner functions as payable**<a name="G-03"></a>

### ***Description:***

- This one is a bit questionable, but you can try that out. So, the compiler adds some extra conditions in case of non-payable, but we know that `onlyOwner` modifier will be reverted, if the user invoke following methods.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file:  src/Kernel.sol
      ...............................
      
        // Lines: [76-78]
            function changeKernel(Kernel newKernel_) external onlyKernel {
                kernel = newKernel_;
            }

        // Lines: [126-128]
            function setActiveStatus(bool activate_) external onlyKernel {
                isActive = activate_;
            }

        // Lines: [439-448]
            function grantRole(Role role_, address addr_) public onlyAdmin {}

        // Lines: [451-460]
            function revokeRole(Role role_, address addr_) public onlyAdmin {}

        // Lines: [235-260]
            function revokeRole(Role role_, address addr_) public onlyAdmin {}

      file: src/policies/BondCallback.sol 
      ...............................

        // Lines: [61-76]
            function requestPermissions()
                external
                view
                override
                onlyKernel
                returns (Permissions[] memory requests)
            {}

      file: src/policies/Governance.sol 
      ...............................

        // Lines: [61-76]
            function requestPermissions()
                external
                view
                override
                onlyKernel
                returns (Permissions[] memory requests)
            {}
        ```

## **[G-04] Use binary shifting instead of `a / 2^x, x > 0` or `a * 2^x, x > 0`**<a name="G-04"></a>

### ***Description:***

- It's also pretty impactful one, especially in loops.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file:  src/policies/Operator.sol
      ...............................
      
        // Lines: [372-372]
          int8 scaleAdjustment = int8(ohmDecimals) - int8(reserveDecimals) + (priceDecimals / 2);

        // Lines: [427-427]
          int8 scaleAdjustment = int8(reserveDecimals) - int8(ohmDecimals) + (priceDecimals / 2);

        // Lines: [419-420]
          uint256 invCushionPrice = 10**(oracleDecimals * 2) / range.cushion.low.price;
          uint256 invWallPrice = 10**(oracleDecimals * 2) / range.wall.low.price;

      ```

## **[G-05] Cache state variables, `MLOAD` << `SLOAD`**<a name="G-05"></a>

### ***Description:***

- `MLOAD` costs only 3 units of gas, `SLOAD`(warm access) is about 100 units. Therefore, cache, when it's possible.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/policies/Governance.sol 
      ...............................

        // Lines: [240-262]
          // Comment: it is possible to cache `activeProposal.proposalId` in order to get MLOAD instead of warm access per each invoking.  

                function vote(bool for_) external {
                  uint256 userVotes = VOTES.balanceOf(msg.sender);

                  if (activeProposal.proposalId == 0) {
                      revert NoActiveProposalDetected();
                  }

                  if (userVotesForProposal[activeProposal.proposalId][msg.sender] > 0) {
                      revert UserAlreadyVoted();
                  }

                  if (for_) {
                      yesVotesForProposal[activeProposal.proposalId] += userVotes;
                  } else {
                      noVotesForProposal[activeProposal.proposalId] += userVotes;
                  }

                  userVotesForProposal[activeProposal.proposalId][msg.sender] = userVotes;

                  VOTES.transferFrom(msg.sender, address(this), userVotes);

                  emit WalletVoted(activeProposal.proposalId, msg.sender, for_, userVotes);
              }

        // Lines: [265-289]
          // Comment: it is possible to cache `activeProposal.proposalId` in order to get MLOAD instead of warm access per each invoking.  

              function executeProposal() external {
                  uint256 netVotes = yesVotesForProposal[activeProposal.proposalId] -
                      noVotesForProposal[activeProposal.proposalId];
                  if (netVotes * 100 < VOTES.totalSupply() * EXECUTION_THRESHOLD) {
                      revert NotEnoughVotesToExecute();
                  }

                  if (block.timestamp < activeProposal.activationTimestamp + EXECUTION_TIMELOCK) {
                      revert ExecutionTimelockStillActive();
                  }

                  Instruction[] memory instructions = INSTR.getInstructions(activeProposal.proposalId);

                  for (uint256 step; step < instructions.length; ) {
                      kernel.executeAction(instructions[step].action, instructions[step].target);
                      unchecked {
                          ++step;
                      }
                  }

                  emit ProposalExecuted(activeProposal.proposalId);

                  // deactivate the active proposal
                  activeProposal = ActivatedProposal(0, 0);
              }

      file:  src/modules/RANGE.sol
      ...............................

        // Lines: [158-178]
          // Comment: here you can allocate the result of `_range.wall.low.price` computation and etc into the memory, so in event you can simply pass that precomputed value instead of taking warm access to get `_range.wal.low.price again`. The same for all computation could be done.

              function updatePrices(uint256 movingAverage_) external permissioned {
                  // Cache the spreads
                  uint256 wallSpread = _range.wall.spread;
                  uint256 cushionSpread = _range.cushion.spread;

                  // Calculate new wall and cushion values from moving average and spread
                  _range.wall.low.price = (movingAverage_ * (FACTOR_SCALE - wallSpread)) / FACTOR_SCALE;
                  _range.wall.high.price = (movingAverage_ * (FACTOR_SCALE + wallSpread)) / FACTOR_SCALE;

                  _range.cushion.low.price = (movingAverage_ * (FACTOR_SCALE - cushionSpread)) / FACTOR_SCALE;
                  _range.cushion.high.price =
                      (movingAverage_ * (FACTOR_SCALE + cushionSpread)) /
                      FACTOR_SCALE;

                  emit PricesChanged(
                      _range.wall.low.price,
                      _range.cushion.low.price,
                      _range.cushion.high.price,
                      _range.wall.high.price
                  );
              }

      file: src/modules/PRICE.sol 
      ...............................

        // Lines: [97-100]
          // Comment: Also cache computation into memory, then assign the numObservations and after that just pass this cached result into allocating array. 

            numObservations = uint32(movingAverageDuration_ / observationFrequency_);
            observations = new uint256[](numObservations);

        // Lines: [144-144]
          // This line could be transformed a bit:
            nextObsIndex = (nextObsIndex + 1) % numObs;

            ++nextObsIndex %= numObs; 

        // Lines: [165-171]
          // Comment: cache `observationFrequency`
              if (updatedAt < block.timestamp - 3 * uint256(observationFrequency))
                  revert Price_BadFeed(address(_ohmEthPriceFeed));
              ohmEthPrice = uint256(ohmEthPriceInt);

              int256 reserveEthPriceInt;
              (, reserveEthPriceInt, , updatedAt, ) = _reserveEthPriceFeed.latestRoundData();
              if (updatedAt < block.timestamp - uint256(observationFrequency))

        // Lines: [240-262]

        ```

## **[G-06] Add `require() before some computations, if it makes sense`**<a name="G-06"></a>


### ***Description:***

- Everyting above `require()` takes some gas for execution, therefore if the statement reverts gas will not be retrieved.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/modules/INSTR.sol 
      ...............................
      
        // Lines: [48-48]
          // Comment: Put right after getting length in order to avoid unnecessary cold/warm accesses in case of the failure. 

            if (length == 0) revert INSTR_InstructionsCannotBeEmpty();

      file: src/policies/Governance.sol 
      ...............................
        // Lines: [183-185]

              if (proposalId_ == 0) {
                  revert CannotEndorseNullProposal();
              }

    ```



## **[G-07] `Internal` functions can be inlined**<a name="G-07"></a>

### ***Description:***

- It takes some extra `JUMP`s which costs around 40-50 gas uints. In loops it will save significant amount of gas.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file:  src/policies/Operator.sol
      ...............................
      
        // Lines: [643-643]
          function _updateRangePrices() internal {}

        // Lines: [634-634]
            function _updateCapacity(bool high_, uint256 reduceBy_) internal {}

    ```



## **[G-08] Use `private/internal` for `constants/immutable` variables instead of `public`**<a name="G-08"></a>

### ***Description:***

- Optimization comes from not creating a getter function for each `public` instance. Try to define them as private/internal if it's possible in specific case.
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file:  src/Kernel.sol
      ...............................
     
        // Lines: [155-158]
            address public executor;
            address public admin;

        // Lines: [165-197]
            Keycode[] public allKeycodes;
            mapping(Keycode => Module) public getModuleForKeycode;
            mapping(Module => Keycode) public getKeycodeForModule;
            mapping(Keycode => Policy[]) public moduleDependents;
            mapping(Keycode => mapping(Policy => uint256)) public getDependentIndex;
            mapping(Keycode => mapping(Policy => mapping(bytes4 => bool))) public modulePermissions;
            Policy[] public activePolicies;
            mapping(Policy => uint256) public getPolicyIndex;
            mapping(address => mapping(Role => bool)) public hasRole;
            mapping(Role => bool) public isRole;

      // Comment: I'll not include every single occurance, because the main message have been proposed. 
    ```



## **[G-09] Use const values instead of `type(uint256).max`**<a name="G-09"></a>

### ***Description:***

- Not sure about readability, but it might be tangible in loops.
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file:  src/modules/RANGE.sol
      ...............................
      
        // Lines: [88-88]
          market: type(uint256).max

        // Lines: [95-95]
          market: type(uint256).max 

        // Lines: [221-221]
          if (market_ == type(uint256).max && marketCapacity_ != 0) revert RANGE_InvalidParams();

        // Lines: [230-230]
          if (market_ == type(uint256).max) {}

      file: src/modules/TRSRY.sol 
      ...............................

        // Lines: [147-151]
            if (approval != type(uint256).max) {
                unchecked {
                    withdrawApproval[withdrawer_][token_] = approval - amount_;
                }
            }

      file: src/policies/BondCallback.sol 
      ...............................

        // Lines: [57-57]
            ohm.safeApprove(address(MINTR), type(uint256).max);

        // Lines: [95-95]
            TRSRY.setApprovalFor(address(this), payoutToken, type(uint256).max);

      file:  src/policies/Operator.sol
      ...............................

        // Lines: [167-167]
            ohm.safeApprove(address(MINTR), type(uint256).max);

        // Lines: [477-477]
            RANGE.updateMarket(high_, type(uint256).max, 0);

        // Lines: [603-603]
            TRSRY.setApprovalFor(address(this), reserve, type(uint256).max);

    ```

## **[G-10] Mark functions as `external` instead of `public`, if there are no internal calls**<a name="G-10"></a>

### ***Description:***

- Functions marked by `external` use call data to read arguments, where `public` will first allocate in local memory and then load them.
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/modules/INSTR.sol 
      ...............................

        // Lines: [28-30]
          function VERSION() public pure override returns (uint8 major, uint8 minor) {
              return (1, 0);
          }

        // Lines: [37-39]
          function getInstructions(uint256 instructionsId_) public view returns (Instruction[] memory) {
              return storedInstructions[instructionsId_];
          }


      file: src/modules/MINTR.sol 
      ...............................

        // Lines: [20-22]
            function KEYCODE() public pure override returns (Keycode) {
                return toKeycode("MINTR");
            }

        // Lines: [33-39]
            function mintOhm(address to_, uint256 amount_) public permissioned {
                ohm.mint(to_, amount_);
            }

            function burnOhm(address from_, uint256 amount_) public permissioned {
                ohm.burnFrom(from_, amount_);
            }


      ```
## **[G-11] Use `calldataload` instead of `mload`**<a name="G-11"></a>

### ***Description:***

- After Berlin hard fork, to load a non-zero byte from calldata dropped from 64 units of gas to 16 units, therefore if you do not modify args, use a calldata instead of memory. Here you need to explicitly specify `calldataload`, or replace `memory` with `calldata`. If the args are pretty huge, allocating args in memory will cost a lot. 
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/Kernel.sol 
      ...............................
      
        // Lines: [391-395]
          // Comment: Permissions[] calldata requests_ instead of allocating into the memory.

            function _setPolicyPermissions(
                Policy policy_,
                Permissions[] memory requests_,
                bool grant_
            ) internal {}

      file: src/modules/PRICE.sol 
      ...............................

        // Lines: [205-205]
            function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_){}

      file:  src/modules/RANGE.sol
      ...............................

        // Lines: [77-81]
            constructor(
                Kernel kernel_,
                ERC20[2] memory tokens_,
                uint256[3] memory rangeParams_ // [thresholdFactor, cushionSpread, wallSpread]
            ) Module(kernel_) {}

        // Lines: [275-277]
          // Comment: Calldata return
            function range() external view returns (Range memory) {
                return _range;
            }

      file: src/policies/BondCallback.sol 
      ...............................

        // Lines: [152-152]
            function batchToTreasury(ERC20[] memory tokens_) external onlyRole("callback_admin") {}

      file: src/policies/Governance.sol 
      ...............................

        // Lines: [159-163]
            function submitProposal(
                Instruction[] calldata instructions_,
                bytes32 title_,
                string memory proposalURI_
            ) external {}

      file:  src/policies/Operator.sol
      ...............................

        // Lines: [92-98]
            constructor(
                Kernel kernel_,
                IBondAuctioneer auctioneer_,
                IBondCallback callback_,
                ERC20[2] memory tokens_, // [ohm, reserve]
                uint32[8] memory configParams // [cushionFactor, cushionDuration, cushionDebtBuffer, cushionDepositInterval, reserveFactor, regenWait, regenThreshold, regenObserve]
            ) Policy(kernel_) {}

        // Lines: [793-801]
            function status() external view override returns (Status memory) {
                return _status;
            }

            /// @inheritdoc IOperator
            function config() external view override returns (Config memory) {
                return _config;
            }
        }

      file: src/policies/PriceConfig.sol 
      ...............................

        // Lines: [45-48]
            function initialize(uint256[] memory startObservations_, uint48 lastObservationTime_)
                external
                onlyRole("price_admin")
            {

        // Lines: [53-53]
            function revokePolicyApprovals(address policy_, ERC20[] memory tokens_) external {}

      ```

## Kudos for the quality of the code! It's pretty easy to explore
