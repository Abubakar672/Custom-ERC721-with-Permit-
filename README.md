# Custom-ERC721-with-Permit-
About The Project 

 

Our ERC721 is an implementation of IERC721 with significant gas savings for minting multiple NFTs in a single transaction. 

This contract will enable minting multiple NFTs for essentially the same cost of minting a single NFT. 

CONTEXT 

We’ve measured the gas costs and prices for minting, comparing OpenZeppelin’s ERC721Enumerable vs our ERC721. In our measurements, the same application-level logic is used, the only difference being the _safeMint function called. 

 

HOW DOES IT WORK? 

We've detailed the three main optimizations we've made below. While we tried to make it as clear as possible  

 

Optimization 1 - Removing duplicate storage from OpenZeppelin’s (OZ) ERC721Enumerable 

 

The widely-used OZ implementation of IERC721Enumerable includes redundant storage of each token’s metadata. This denormalized approach optimizes for read functions at a significant cost to write functions, which isn’t ideal given that users are much less likely to pay for read functions. Additionally, the fact that our tokens are serially numbered starting from 0 lets us remove some redundant storage from the base implementation. 

 

Optimization 2 - updating the owner’s balance once per batch mint request, instead of per minted NFT 

 

Suppose Ali has 2 tokens and wants to buy 5 more. In Solidity, it costs gas to update a stored value. Therefore, if we are tracking in storage how many tokens Ali owns, it would be cheaper to update Ali holdings from 2 directly to 7 with one update, instead of updating that value 5 times (once per additional token, from 2 to 3, 3 to 4, etc). 

 

Optimization 3 - updating the owner data once per batch mint request, instead of per minted NFT 

 

This is similar in spirit to optimization 2. Suppose Ali wanted to buy 3 tokens - token #100, #101, and #102. Instead of saving Ali as the owner 3 times (each time costing us gas), we can instead save the owner value just once in a way that semantically implies that Ali owns all 3 of those tokens. 

 

you can use the contracts in the library by importing them: 

pragma solidity ^0.8.4; 

import "erc721a/contracts/ERC721.sol"; 

contract ERC721main is ERC721 { 

    constructor() ERC721A("Main", "M") { } 

 

    function mint(uint256 quantity) external payable { 

        _mint(msg.sender, quantity); 

    } 

} 
