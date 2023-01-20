  GAS  OPTIMIZATION RELATED
 1) G-1:

   Description :  Use fixed size bytes array rather than string or bytes[] for smaller naming convention such as contract name(#27)
               :   If the string you are dealing with can be limited to max of 32 characters, use bytes[32] instead of dynamic bytes array or string

		  
                 Total No. of Instances Found: 8

                 For Example: 
	              
				 string a; -> bytes32 a;
				   
				   
				   
		      FileName: contracts/p1/RToken.sol
		      Line No : 149,150
			  
			  FileName: contracts/p1/StRSR.sol
		      Line No : 42, 43
			  
			  
			  FileName: contracts/p1/Deployer.sol
		      Line No : 103 , 104 , 206 , 207
			  
               
	   	 
		
		
  2) G-2:
     
 Description :  Use unchecked for arithmetic where you are sure that 'max' parameter won't over or underflow (#28)
	 

          Total No. of Instances Found: 7

           For Example: 
		```   
		   function loop(uint256 length) public {
      	   for (uint256 i = 0; i < length; ) {
        	// do something
       	   unchecked {
        	i++;
        	}
      	  }
              ```
			  
			  
		As we see from line no.34 , filename : p1/Distributor.sol

        uint8 public constant MAX_DESTINATIONS_ALLOWED = 100;
        so , max length the destination could go is upto 100.	

        for (uint256 i = 0; i < destinations.length(); ++i) {}		
			  
	    ----------------
		FileName: p1/Distributor.sol
		Line No : 619 ,143 
		
	    FileName: p1/BackingManager.sol
		Line No : 221 ,238
		
		FileName: p1/AssetRegistry.sol
		Line No : 38 ,49 , 138 ,
		
		
		

               
   
4) G-3:
   Description :      Booleans use 8 bits while you only need 1 bit  (#60)
	                  Under the hood of solidity, Booleans (bool) are uint8 which means they use 8 bits of storage. A Boolean can only have two values: True or False. 
	                  This means that you can store a boolean in only a single bit.
	                  You can pack 256 booleans in a single word. The easiest way is to take a uint256 variable and use all 256 bits of it to represent individual booleans.
	 

          Total No. of Instances Found: 2
	    ----------------
		FileName: contracts/p1/RToken.sol
		Line No : 500
		
		FileName: contracts/p1/Broker.sol
		Line No : 41 
		