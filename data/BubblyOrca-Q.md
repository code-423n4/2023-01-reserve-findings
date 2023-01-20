Under/Overflow vulnerability could be fixed with unchecked, Distributor.sol, Line 128

Current code: 

numTransfers++;

Fixed code:

unchecked {
numTransfers++;
}