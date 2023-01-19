Title: Use of older version of Solidity in PermitLib library

Severity: Medium

Description:
The PermitLib library uses an older version of Solidity. This increases the risk of known vulnerabilities being present in the code, as newer versions of the compiler include security updates and fixes.

Impact:
This issue may cause security vulnerabilities if the library is vulnerable to known attacks. An attacker could exploit a known vulnerability to gain unauthorized access to the smart contract and steal funds, manipulate data or cause other unintended consequences.

Steps to Reproduce:

Review the PermitLib library code
Observe the version of solidity used in the library
Verify that the version used is older than the latest version
Recommendation:
To fix this issue, the library should be updated to use the latest version of Solidity. This can be done by updating the pragma statement in the library code.

Testing:

Verify the library after updating to the latest version of solidity
Test the smart contract and check if it's working as expected
Review the overall impact of the library on the smart contract