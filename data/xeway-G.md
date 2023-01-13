Hello!

Here are small gas optimizations :

- Found in the `allUnique(IERC20[] memory arr)` function of the `ArrayLib` contract (`libraries/Array.sol`) :
l. 10 => `uint256 arrLen = arr.length`. This line is not necessary because we add a variable to the stack (which costs gas) and this variable is only used one time + doesn't necessarily help for the readability of the code.
Therefore, we can replace l. 11 `for (uint256 i = 1; i < arrLen; ++i) {` by `for (uint256 i = 1; i < arr.length; ++i) {` and delete l.10.
 I tested it on Remix and it saved me `7` gas whatever the size of the array.
The updated function looks like this :
```solidity
function allUnique(uint[] memory arr) public pure returns (bool) {
        for (uint256 i = 1; i < arr.length; ++i) {
            for (uint256 j = 0; j < i; ++j) {
                if (arr[i] == arr[j]) return false;
            }
        }
        return true;
}
```

- Found in the `toLower(string memory str)` function of the `StringLib` contract (`libraries/String.sol`) :
l. 13 => `bytes memory bLower = new bytes(bStr.length);`. This array is useless because we can directly modify the `bStr` array. Creating an array in memory (and manipulating it) costs gas, especially if the string of the argument is long. Also, the string in of the parameter can be declared in calldata instead of memory. It costs less gas.
So the updated function looks like this :
```solidity
function toLower(string calldata str) internal pure returns (string memory) {
        bytes memory bStr = bytes(str);
        for (uint256 i = 0; i < bStr.length; i++) {
            // Uppercase character...
            if ((uint8(bStr[i]) >= 65) && (uint8(bStr[i]) <= 90)) {
                // So we add 32 to make it lowercase
                bStr[i] = bytes1(uint8(bStr[i]) + 32);
            }
        }
        return string(bStr);
}
```
On Remix, I tested the new function by passing the string "HELLO WorLD", and it saved me `939` gas.

I hope this will help, thank you for your attention, and good luck with your project!