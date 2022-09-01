# INFORMATIONAL SEVERITY ISSUES

**Change Function Visibilities**

**Description:**
 Some functions in the contract are designed with a public function without being called internally within the contract. These sets of function with the public visibility costs will cost more call during interaction. The function shares the same name across modules. 

The functions are:
KEYCODE
VERSION

**Remediation:**
 For gas optimization, it’s recommended to make these functions external since they were not directly used within the contract. 