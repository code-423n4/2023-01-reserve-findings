## Vulnerability:
_register and _registerIgnoringCollisions are internal functions and not described in the contract.

## Impact:

The internal functions _register and _registerIgnoringCollisions are not described in the contract, making it unclear what their purpose is and how they are supposed to be used.
This can lead to confusion and errors in other parts of the system that rely on these functions.
It can also make it more difficult for external auditors and developers to understand and verify the contract's behavior.

## Recommendation:

Add proper documentation and comments to the internal functions _register and _registerIgnoringCollisions to explain their purpose and behavior.
Clearly describe the function signature, parameters and return values, and any other relevant details.
Consider making these functions external if they are intended to be called by other contracts or external users.
If the functions are not intended to be used outside of the contract, consider making them private to prevent accidental or unauthorized use.
Steps to Reproduce:

Look at the contract code.
Observe that the _register and _registerIgnoringCollisions functions are internal and not described in the contract.
Affected Version:
This vulnerability exists in the provided codebase with the version