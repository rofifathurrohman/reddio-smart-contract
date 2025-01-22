🚀 Deploy Smart Contract on Reddio Sepolia Testnet
This repository demonstrates how to deploy a simple Solidity smart contract on the Reddio Sepolia Testnet using Hardhat and Ethers.js.

Prerequisites
Make sure you have the following installed:

Node.js (LTS version recommended)
npm
Hardhat
A wallet with Sepolia testnet ETH (get it from Sepolia Faucet)
Project Setup
Clone this repository or create a new folder:

bash
Copy
Edit
mkdir reddio-deployment
cd reddio-deployment
npm init -y
Install Hardhat:

bash
Copy
Edit
npm install --save-dev hardhat
Create a new Hardhat project:

bash
Copy
Edit
npx hardhat
Choose Create a basic sample project and follow the prompts.

Install required dependencies:

bash
Copy
Edit
npm install --save-dev @nomicfoundation/hardhat-toolbox ethers dotenv
Configuration
Create a .env file in the root of your project to store your private key:

plaintext
Copy
Edit
PRIVATE_KEY=your-wallet-private-key
Open hardhat.config.js and update it with the following configuration:

javascript
Copy
Edit
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config();

module.exports = {
  solidity: "0.8.18",
  networks: {
    reddio: {
      url: "https://reddio-dev.reddio.com", // Reddio Sepolia Testnet RPC URL
      chainId: 50341,                      // Reddio chain ID
      accounts: [process.env.PRIVATE_KEY], // Wallet private key from .env file
    },
  },
};
Note: Add .env to your .gitignore file to ensure the private key is not exposed in your repository.

Write the Smart Contract
Go to the contracts/ folder and create a file called SimpleStorage.sol:

bash
Copy
Edit
touch contracts/SimpleStorage.sol
Add the following code to SimpleStorage.sol:

solidity
Copy
Edit
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SimpleStorage {
    uint256 public storedData;

    function set(uint256 x) public {
        storedData = x;
    }

    function get() public view returns (uint256) {
        return storedData;
    }
}
Compile the Smart Contract
Compile the smart contract to generate the required artifacts:

bash
Copy
Edit
npx hardhat compile
Write the Deployment Script
Create a deployment script in the scripts/ folder:

bash
Copy
Edit
touch scripts/deploy.js
Add the following code to deploy.js:

javascript
Copy
Edit
const hre = require("hardhat");
const { formatEther } = require("ethers");

async function main() {
  // Get the deployer's account
  const [deployer] = await hre.ethers.getSigners();

  console.log("Deploying contract with the account:", deployer.address);

  // Get the deployer's balance
  const balance = await deployer.provider.getBalance(deployer.address);
  console.log("Account balance:", formatEther(balance), "ETH");

  // Compile and deploy the contract
  const SimpleStorage = await hre.ethers.getContractFactory("SimpleStorage");
  const simpleStorage = await SimpleStorage.deploy();

  console.log("SimpleStorage deployed to:", simpleStorage.target); // Get the contract address
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
Deploy the Contract
Deploy the smart contract to the Reddio Sepolia Testnet:

bash
Copy
Edit
npx hardhat run scripts/deploy.js --network reddio
If successful, the output should look like this:

yaml
Copy
Edit
Deploying contract with the account: 0xYourWalletAddress
Account balance: 1.234 ETH
SimpleStorage deployed to: 0xDeployedContractAddress
Verify the Deployment
Copy the deployed contract address from the output.
Open the Reddio Explorer.
Search for the deployed contract address to verify it was successfully deployed.
Testing the Contract
You can test your contract by writing a script to interact with it or by using Hardhat console:

Open the Hardhat console:

bash
Copy
Edit
npx hardhat console --network reddio
Interact with the contract:

javascript
Copy
Edit
const SimpleStorage = await ethers.getContractFactory("SimpleStorage");
const contract = SimpleStorage.attach("0xDeployedContractAddress"); // Replace with your contract address

// Set a value
await contract.set(42);

// Get the value
const value = await contract.get();
console.log("Stored value:", value.toString());
Troubleshooting
If you encounter issues during deployment:

Make sure your .env file contains the correct private key.
Ensure you have Sepolia testnet ETH in your wallet. Use a Sepolia Faucet if needed.
Double-check the configuration in hardhat.config.js.
If the problem persists, feel free to open an issue in this repository.

References
Hardhat Documentation
Reddio Documentation
Ethers.js Documentation
License
This project is licensed under the MIT License. See the LICENSE file for more information.
