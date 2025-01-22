# 🚀 Deploy Smart Contract on Reddio Sepolia Testnet

This repository demonstrates how to deploy a simple Solidity smart contract on the Reddio Sepolia Testnet using Hardhat and Ethers.js.

## Prerequisites

Make sure you have the following installed:

- Node.js (LTS version recommended)
- npm
- Hardhat
- A wallet with Sepolia testnet ETH (get it from [Sepolia Faucet](https://faucet.sepolia.dev))

## Project Setup

1. Clone this repository or create a new folder:

   ```bash
   mkdir reddio-deployment
   cd reddio-deployment
   npm init -y
   ```

2. Install Hardhat:

   ```bash
   npm install --save-dev hardhat
   ```

3. Create a new Hardhat project:

   ```bash
   npx hardhat
   ```

   Choose `Create a basic sample project` and follow the prompts.

4. Install required dependencies:

   ```bash
   npm install --save-dev @nomicfoundation/hardhat-toolbox ethers dotenv
   ```

## Configuration

1. Create a `.env` file in the root of your project to store your private key:

   ```plaintext
   PRIVATE_KEY=your-wallet-private-key
   ```

2. Open `hardhat.config.js` and update it with the following configuration:

   ```javascript
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
   ```

3. Add `.env` to your `.gitignore` file to ensure the private key is not exposed in your repository.

## Write the Smart Contract

1. Go to the `contracts/` folder and create a file called `SimpleStorage.sol`:

   ```bash
   touch contracts/SimpleStorage.sol
   ```

2. Add the following code to `SimpleStorage.sol`:

   ```solidity
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
   ```

## Compile the Smart Contract

Compile the smart contract to generate the required artifacts:

```bash
npx hardhat compile
```

## Write the Deployment Script

1. Create a deployment script in the `scripts/` folder:

   ```bash
   touch scripts/deploy.js
   ```

2. Add the following code to `deploy.js`:

   ```javascript
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
   ```

## Deploy the Contract

Deploy the smart contract to the Reddio Sepolia Testnet:

```bash
npx hardhat run scripts/deploy.js --network reddio
```

If successful, the output should look like this:

```yaml
Deploying contract with the account: 0xYourWalletAddress
Account balance: 1.234 ETH
SimpleStorage deployed to: 0xDeployedContractAddress
```

## Verify the Deployment

1. Copy the deployed contract address from the output.
2. Open the [Reddio Explorer](https://reddio-explorer.io).
3. Search for the deployed contract address to verify it was successfully deployed.

## Testing the Contract

You can test your contract by writing a script to interact with it or by using the Hardhat console:

1. Open the Hardhat console:

   ```bash
   npx hardhat console --network reddio
   ```

2. Interact with the contract:

   ```javascript
   const SimpleStorage = await ethers.getContractFactory("SimpleStorage");
   const contract = SimpleStorage.attach("0xDeployedContractAddress"); // Replace with your contract address

   // Set a value
   await contract.set(42);

   // Get the value
   const value = await contract.get();
   console.log("Stored value:", value.toString());
   ```

## Troubleshooting

If you encounter issues during deployment:

- Make sure your `.env` file contains the correct private key.
- Ensure you have Sepolia testnet ETH in your wallet. Use a [Sepolia Faucet](https://faucet.sepolia.dev) if needed.
- Double-check the configuration in `hardhat.config.js`.

If the problem persists, feel free to open an issue in this repository.

## References

- [Hardhat Documentation](https://hardhat.org/docs)
- [Reddio Documentation](https://reddio-docs.io)
- [Ethers.js Documentation](https://docs.ethers.io/v5/)

## License

This project is licensed under the MIT License. See the LICENSE file for more information.
