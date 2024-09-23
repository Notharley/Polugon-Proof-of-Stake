# POLYGON PROOF OF STAKE 

# Description [MetaToken.sol file]

This Solidity code defines a smart contract called MetaToken, which is an ERC-721 compliant non-fungible token (NFT) contract.

## Getting Started

### Executing program

To run this program, I have used VS Code.

SMART CONTRACT CODE:

```solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

```
The contract starts by specifying the SPDX License Identifier and the Solidity compiler version it is compatible with.

```solidity

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

```
The contract imports necessary libraries from OpenZeppelin, which are required for implementing the ERC-721 standard and other functionalities.

```solidity

contract MetaToken is ERC721, Ownable {

```
The MetaToken contract is declared. It inherits from ERC721 (ERC-721 token standard) and Ownable (provides an ownership control mechanism).

```solidity

using Counters for Counters.Counter;

````
Inside the contract, a using statement is used to enable the usage of the Counters library, which will help keep track of the token IDs.

```solidity

Counters.Counter private _tokenIds;

```
The contract creates a private Counters.Counter variable called _tokenIds to keep track of the token IDs. This variable will be used to assign unique IDs to each token when minting.

```solidity

constructor() ERC721("Spiderman", "dj") {}

```
The contract's constructor is defined, which sets the name and symbol for the NFT token. In this case, the NFT's name is set to "Spiderman" and its symbol to "dj".

```solidity

string[] uri = [...];
string[] prompt = [...];

```
Two string arrays are declared: uri and prompt. uri stores URLs pointing to the metadata of each token, and prompt stores descriptions associated with each token.

```solidity

function mint(address to) public onlyOwner returns (uint256) {
    _tokenIds.increment();
    uint256 tokenId = _tokenIds.current();
    _safeMint(to, tokenId);
    return tokenId;
}

```
The mint function is defined with the onlyOwner modifier. This function is used to mint new tokens and assign them to a specific address (to). Each time a new token is minted, the _tokenIds counter is incremented to assign a unique token ID to the newly created token.


# Description [bridge.js file]

This code is a JavaScript script that interacts with Ethereum smart contracts using Hardhat, an Ethereum development environment. 

## Getting Started

### Executing program

To run this program, I have used VS Code.
```
//terminal code to run
npx hardhat run scripts/dbridge.js --network goerli
````

```javascript

const hre = require("hardhat");
const tokenJSON = require("../artifacts/contracts/MetaToken.sol/MetaToken.json");

const tokenAddress = "0x5b6b8c88B89149f920D674B844bd79154B5a9AD9" ; 
const tokenABI = tokenJSON.abi;
const walletAddress = "0xAABFa817c27bd98fb75DF05Cdc27E631d315852e";

const fxRootContractABI = require("../fxRootContractABI.json");
const fxERC21RootAddress = "0xF9bc4a80464E48369303196645e876c8C7D972de";

async function main() {
    const token = await hre.ethers.getContractAt(tokenABI, tokenAddress);

    const fxContract = await hre.ethers.getContractAt(fxRootContractABI, fxERC21RootAddress);
    const approveTx = await token.setApprovalForAll(fxERC21RootAddress, true);
    await approveTx.wait();

    console.log("Approval confirmed");
    for (let i = 1; i < 6; i++) {
    const depositTx = await fxContract.deposit(tokenAddress,walletAddress, i,'0x6566');
    await depositTx.wait();
    }
    console.log("Tokens deposited");  
  }
  main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
  });

```
The script starts by importing the Hardhat Runtime Environment (hre) and the JSON representation of the contract's ABI (tokenJSON). The contract's ABI is used to interact with the contract's functions.

tokenAddress is defined as the Ethereum address of the MetaToken contract. tokenABI is set to the ABI of the MetaToken contract.

walletAddress is defined as a public Ethereum address, representing the address of the wallet where tokens will be deposited.

fxRootContractABI is required from an external JSON file, and fxERC21RootAddress is set to the Ethereum address of the fxRoot contract (assumed to be an ERC-20 to ERC-721 bridge).

The main function is declared as an asynchronous function, which will be the entry point of the script.

Inside the main function, two Ethereum contracts are retrieved using their ABIs and addresses: token (instance of the MetaToken contract) and fxContract (instance of the fxRoot contract).

The script calls token.setApprovalForAll(fxERC21RootAddress, true) to approve the fxRoot contract to spend the owner's tokens. This allows the fxRoot contract to transfer the tokens on the owner's behalf. The approval transaction is then confirmed.

The script then enters a loop (5 iterations) to deposit tokens from the MetaToken contract to the fxRoot contract. Each iteration deposits one token with a specified token ID (i) and additional data ("0x6566"). The deposit transaction is confirmed after each iteration.

After all tokens are deposited, the script logs "Tokens deposited."

The script uses the main().catch() pattern to handle errors in the asynchronous function.


# Description [deploy.js file]

This code is a JavaScript script that uses Hardhat, an Ethereum development environment, to deploy a smart contract. 

## Getting Started

### Executing program

To run this program, I have used online VS Code.

```
//terminal code to run
npx hardhat run scripts/deploy.js --network goerli
````

```javascript

const hre = require("hardhat");

async function main() {

  const token = await hre.ethers.deployContract("MetaToken");

  console.log("Token address:", await token.getAddress());
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});

```
The script starts by importing the Hardhat Runtime Environment (hre). This import is used to access Hardhat's functionalities for deploying contracts.

The main function is declared as an asynchronous function, which will be the entry point of the script.

Inside the main function, the hre.ethers.deployContract function is called to deploy a contract named "MetaToken." This contract deployment is an asynchronous operation and returns a contract object (token).

The script then logs the Ethereum address of the deployed contract using await token.getAddress(). This address represents the location of the deployed contract on the Ethereum blockchain.

The script uses the main().catch() pattern to handle errors in the asynchronous function.


# Description [getBalance.js file]

This code is a JavaScript script that uses Hardhat, an Ethereum development environment, to interact with an ERC721 token contract.  

## Getting Started

### Executing program

To run this program, I have used online VS Code.
```
//terminal code to run
npx hardhat run scripts/deploy.js --network mumbai
````

```javascript

const hre = require("hardhat");
const tokenContractJSON = require("../artifacts/contracts/MetaToken.sol/MetaToken.json");
require('dotenv').config()

const tokenAddress = "0x5b6b8c88B89149f920D674B844bd79154B5a9AD9"; 
const tokenABI = tokenContractJSON.abi;
const walletAddress = "0xAABFa817c27bd98fb75DF05Cdc27E631d315852e"; 

async function main() {

    const token = await hre.ethers.getContractAt(tokenABI, tokenAddress);
    console.log("You now have: " + await token.balanceOf(walletAddress) + " tokens");
  }
  
  main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
  });

```
The script starts by importing the Hardhat Runtime Environment (hre). This import is used to access Hardhat's functionalities for interacting with Ethereum contracts.

The script requires the JSON representation of the contract's ABI (tokenContractJSON) for the ERC-20 token contract.

It uses the dotenv library to load environment variables from a .env file. However, the .env file is not explicitly shown in the code provided, so the actual environment variables are not visible.

tokenAddress is defined as the Ethereum address of the ERC-20 token contract, and tokenABI is set to the ABI of the ERC-20 token contract.

walletAddress is defined as a public Ethereum address, representing the address for which the script will check the token balance.

The main function is declared as an asynchronous function, which will be the entry point of the script.

Inside the main function, the script uses hre.ethers.getContractAt to get an instance of the ERC-20 token contract at the specified tokenAddress.

The script calls the balanceOf function of the ERC-20 token contract to check the token balance of the specified walletAddress.

The script then logs the token balance for the provided wallet address.

The script uses the main().catch() pattern to handle errors in the asynchronous function.


# Description [mint.js file]

This code is a JavaScript script that uses Hardhat, an Ethereum development environment, to interact with an ERC721 token contract called "MetaToken" and perform a series of minting operations.

## Getting Started

### Executing program

To run this program, I have used online VS Code.

```
//terminal code to run
npx hardhat run scripts/mint.js --network goerli
````

```javascript

const hre = require("hardhat");
const tokenContractJSON = require("../artifacts/contracts/MetaToken.sol/MetaToken.json");
require('dotenv').config()

const tokenAddress = "0x5b6b8c88B89149f920D674B844bd79154B5a9AD9"; // place your erc20 contract address here
const tokenABI = tokenContractJSON.abi;
const walletAddress = "0xAABFa817c27bd98fb75DF05Cdc27E631d315852e"; // place your public address for your wallet here

async function main() {

    const token = await hre.ethers.getContractAt(tokenABI, tokenAddress);
    for(let i=0; i<5; i++)
    {
    const tx = await token.mint(walletAddress);
    await tx.wait();
    }
    console.log("You now have: " + await token.balanceOf(walletAddress) + " tokens");
  }
  
  main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
  });

```
The script starts by importing the Hardhat Runtime Environment (hre). This import is used to access Hardhat's functionalities for interacting with Ethereum contracts.

The script requires the JSON representation of the contract's ABI (tokenContractJSON) for the ERC-20 token contract.

It uses the dotenv library to load environment variables from a .env file. However, the .env file is not explicitly shown in the code provided, so the actual environment variables are not visible.

tokenAddress is defined as the Ethereum address of the ERC-20 token contract, and tokenABI is set to the ABI of the ERC-20 token contract.

walletAddress is defined as a public Ethereum address, representing the address to which the minted tokens will be assigned.

The main function is declared as an asynchronous function, which will be the entry point of the script.

Inside the main function, the script uses hre.ethers.getContractAt to get an instance of the ERC-20 token contract at the specified tokenAddress.

The script enters a loop that executes five times, representing five minting operations. It calls the mint function of the ERC-20 token contract, minting new tokens and assigning them to the specified walletAddress. Each minting operation is confirmed by waiting for the transaction receipt.

After the loop, the script retrieves the token balance of the specified walletAddress using the balanceOf function of the ERC-20 token contract.

The script then logs the token balance for the provided wallet address.

The script uses the main().catch() pattern to handle errors in the asynchronous function.


# Description [prompt.js file]

This code is a JavaScript script that uses Hardhat, an Ethereum development environment, to interact with an ERC-721 token contract called "MetaToken" and retrieve the descriptions of tokens with specific token IDs.

## Getting Started

### Executing program

To run this program, I have used online VS Code.
```
//terminal code to run
npx hardhat run scripts/prompt.js --network goerli
````

```javascript

const hre = require("hardhat");
const tokenContractJSON = require("../artifacts/contracts/MetaToken.sol/MetaToken.json");
require('dotenv').config()

const tokenAddress = "0x5b6b8c88B89149f920D674B844bd79154B5a9AD9"; 
const tokenABI = tokenContractJSON.abi;
const walletAddress = "0xAABFa817c27bd98fb75DF05Cdc27E631d315852e"; 

async function main() {

    const token = await hre.ethers.getContractAt(tokenABI, tokenAddress);
    for(let i=0; i<5; i++)
    {
        const desc =await token.promptDescription(i)
        console.log("TokenID- "+ i+  ": " + desc );
    }
  }
  
  main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
  });

```
The script starts by importing the Hardhat Runtime Environment (hre). This import is used to access Hardhat's functionalities for interacting with Ethereum contracts.

The script requires the JSON representation of the contract's ABI (tokenContractJSON) for the ERC-721 token contract.

It uses the dotenv library to load environment variables from a .env file. However, the .env file is not explicitly shown in the code provided, so the actual environment variables are not visible.

tokenAddress is defined as the Ethereum address of the ERC-721 token contract, and tokenABI is set to the ABI of the ERC-721 token contract.

walletAddress is defined as a public Ethereum address, which is not used in this script.

The main function is declared as an asynchronous function, which will be the entry point of the script.

Inside the main function, the script uses hre.ethers.getContractAt to get an instance of the ERC-721 token contract at the specified tokenAddress.

The script enters a loop that executes five times, representing five token IDs (i). For each token ID, the script calls the promptDescription function of the ERC-721 token contract to retrieve the token's description. The description is then logged to the console along with its corresponding token ID.

The script uses the main().catch() pattern to handle errors in the asynchronous function.

## Authors

Divij Shukla
divij.shukla2003@gmail.com

## License

This project is licensed under the MIT License - see the License.md file for details.
