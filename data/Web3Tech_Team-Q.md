

  1) INFO-1:
  
    Description: Proper Ordering of Functions (#68)
                        Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.


       Functions should be grouped according to their visibility and ordered:

	          1.  constructor

	          2.  receive function (if exists)

	          3.  fallback function (if exists)

	          4.  external

	          5.  public

	          6.  internal

	          7.  private

	Source :   https://docs.soliditylang.org/en/latest/style-guide.html
	
	
	    Total No. of Files Found without above order: 2
	    ----------------
		FileName: contracts/p1/RToken.sol
		
		
		FileName: contracts/p1/StRSRVotes.sol
		
		
		
		
  1) INFO-2:
  
    Description: Ordering of Functions (#77)
	
	It is recommended that layout of contract elements should be in the following order:
	
	
	1. Pragma statements

	2. Import statements

	3. Interfaces

	4. Libraries

	5. Contracts
   		   

	Source :   https://docs.soliditylang.org/en/latest/style-guide.html
	
	
	    Total No. of Files Found without above order: 1
	    ----------------
		FileName: contracts/p1/BasketHandler.sol
		
		Here, libaray defination should be above struct defination
		
		

		