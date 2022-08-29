# 2022-08-olympus-code4rena QA Report

- [2022-08-olympus-code4rena QA Report](#2022-08-olympus-code4rena-qa-report)
  - [Files Description Table](#files-description-table)
  - [QA Report](#qa-report)
  - [Issues found](#issues-found)
    - [[N-01]: Typos](#n-01-typos)
      - [Impact](#impact)
      - [Code Affected and Mitigation](#code-affected-and-mitigation)
      - [Tools used](#tools-used)

##  Files Description Table

| File Name                             | SHA-1 Hash                               |
| ------------------------------------- | ---------------------------------------- |
| 2022-08-olympus/src/modules/PRICE.sol | eb3c920eaaf30e31cffbef13d8510dc18341d5ab |

##  QA Report

## Issues found

### [N-01]: Typos

#### Impact
None.

#### Code Affected and Mitigation

```diff
diff --git a/src/modules/PRICE.sol b/src/modules/PRICE.sol
index 55d85d3..c3867d1 100644
--- a/src/modules/PRICE.sol
+++ b/src/modules/PRICE.sol
@@ -123,7 +123,7 @@ contract OlympusPrice is Module {
         // Revert if not initialized
         if (!initialized) revert Price_NotInitialized();
 
-        // Cache numbe of observations to save gas.
+        // Cache number of observations to save gas.
         uint32 numObs = numObservations;
 
         // Get earliest observation in window
```

#### Tools used
VS Code
