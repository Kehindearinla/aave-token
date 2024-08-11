# DeFi Automation Script Overview

## Overview

This script automates the process of swapping USDC for LINK using Uniswap and then depositing the LINK tokens into the Aave Lending Pool. It interacts with several DeFi protocols to handle token approval, token swapping, and yield farming.

## Key Functions and Logic

### 1. `approveToken(tokenAddress, tokenABI, amount, wallet)`

- **Purpose**: Approves the Swap Router contract to spend a specified amount of USDC tokens.
- **Interaction**: Calls the `approve` method of the ERC20 token contract.

### 2. `getPoolInfo(factoryContract, tokenIn, tokenOut)`

- **Purpose**: Retrieves the Uniswap pool address for swapping USDC to LINK and fetches pool details.
- **Interaction**: Queries the factory contract for the pool address and fetches pool parameters (tokens and fee).

### 3. `prepareSwapParams(poolContract, signer, amountIn)`

- **Purpose**: Prepares the parameters required for the token swap transaction.
- **Interaction**: Constructs parameters for the `exactInputSingle` method of the Swap Router contract.

### 4. `executeSwap(swapRouter, params, signer)`

- **Purpose**: Executes the swap from USDC to LINK.
- **Interaction**: Calls the `exactInputSingle` method on the Swap Router contract with prepared parameters.

### 5. `supplyToAave(tokenAddress, amount, signer)`

- **Purpose**: Supplies LINK tokens to the Aave Lending Pool for earning yield.
- **Interaction**: 
  - **Step 1**: Approves Aave to spend LINK tokens.
  - **Step 2**: Calls the `deposit` method of the Aave Lending Pool contract to deposit the tokens.

## Interaction with DeFi Protocols

- **ERC20 Token Approval**: Interacts with the token contract (e.g., USDC) to allow the Swap Router to spend tokens.
- **Uniswap Pool**: Uses the factory contract to get the pool address and parameters for swapping tokens.
- **Swap Router**: Executes the swap from USDC to LINK using Uniswap’s liquidity pool.
- **Aave Lending Pool**: Approves and deposits LINK tokens into Aave to earn interest.

## Diagram Illustration
## DeFi Automation Script Flow Diagram

```
+---------------------+
| 1. Approve USDC    |
+---------------------+
          |
          v
+---------------------+
| 2. Get Uniswap Pool |
|    Info             |
+---------------------+
          |
          v
+---------------------+
| 3. Prepare Swap     |
|    Params           |
+---------------------+
          |
          v
+---------------------+
| 4. Execute Swap     |
+---------------------+
          |
          v
+---------------------+
| 5. Approve LINK     |
|    for Aave         |
+---------------------+
          |
          v
+---------------------+
| 6. Deposit LINK     |
|    to Aave          |
+---------------------+
```
## Code Explanation


### 1. Import Dependencies and Setup

```javascript
require('dotenv').config();
const { ethers } = require('ethers');
const USDC_ABI = require('./abis/usdc.json');
const LINK_ABI = require('./abis/link.json');
const POOL_FACTORY_ABI = require('./abis/poolFactory.json');
const SWAP_ROUTER_ABI = require('./abis/swapRouter.json');
const AAVE_ABI = require('./abis/aave.json');
```

**Function:**
- **dotenv:** Loads environment variables from a `.env` file, allowing you to securely manage sensitive data like API keys and private keys.
- **ethers:** A library for interacting with the Ethereum blockchain, including smart contracts and transactions.
- **ABI files:** JSON files that describe the interface of Ethereum smart contracts, necessary for interacting with them.

### 2. Setup Provider and Signer

```javascript
const provider = new ethers.providers.JsonRpcProvider(process.env.RPC_URL);
const wallet = new ethers.Wallet(process.env.PRIVATE_KEY, provider);
```

**Function:**
- **provider:** Connects to the Ethereum network using a JSON-RPC URL.
- **wallet:** Creates an instance of the wallet using a private key, which is used to sign transactions and interact with the blockchain.

### 3. Define Contract Addresses

```javascript
const POOL_FACTORY_CONTRACT_ADDRESS = '0x...'; // Uniswap Pool Factory address
const SWAP_ROUTER_CONTRACT_ADDRESS = '0x...'; // Uniswap Swap Router address
const AAVE_LENDING_POOL_ADDRESS = '0x...';   // Aave Lending Pool address
```

**Function:**
- Defines the Ethereum addresses for the Uniswap Pool Factory, Swap Router, and Aave Lending Pool contracts. These addresses are needed to interact with these contracts.

### 4. Define Key Functions

#### `approveToken(tokenAddress, tokenABI, amount, wallet)`

```javascript
async function approveToken(tokenAddress, tokenABI, amount, wallet) {
    const tokenContract = new ethers.Contract(tokenAddress, tokenABI, wallet);
    const tx = await tokenContract.approve(SWAP_ROUTER_CONTRACT_ADDRESS, amount);
    await tx.wait();
}
```

**Function:**
- **Creates a contract instance:** Initializes an ERC20 token contract using its address and ABI.
- **Approval:** Sends a transaction to approve the Swap Router contract to spend a specified amount of the token (USDC) on behalf of the user.
- **Waits for confirmation:** Waits for the approval transaction to be mined and confirmed.

#### `getPoolInfo(factoryContract, tokenIn, tokenOut)`

```javascript
async function getPoolInfo(factoryContract, tokenIn, tokenOut) {
    const poolAddress = await factoryContract.getPool(tokenIn, tokenOut, 3000);
    const poolContract = new ethers.Contract(poolAddress, POOL_ABI, wallet);
    const poolDetails = await poolContract.getPoolDetails();
    return poolDetails;
}
```

**Function:**
- **Get Pool Address:** Queries the Uniswap Pool Factory contract to get the address of the pool for the specified token pair (USDC and LINK).
- **Pool Details:** Fetches details from the pool contract, such as token addresses and fees.

#### `prepareSwapParams(poolContract, signer, amountIn)`

```javascript
function prepareSwapParams(poolContract, signer, amountIn) {
    return {
        tokenIn: '0x...', // USDC address
        tokenOut: '0x...', // LINK address
        fee: 3000,
        recipient: signer.address,
        deadline: Math.floor(Date.now() / 1000) + 60 * 20, // 20 minutes from now
        amountIn: ethers.utils.parseUnits(amountIn.toString(), 6),
        amountOutMinimum: 0,
    };
}
```

**Function:**
- **Construct Parameters:** Prepares parameters for the `exactInputSingle` method of the Uniswap Swap Router contract, including the addresses of tokens, fee, recipient, and transaction details.

#### `executeSwap(swapRouter, params, signer)`

```javascript
async function executeSwap(swapRouter, params, signer) {
    const swapRouterContract = new ethers.Contract(swapRouter, SWAP_ROUTER_ABI, signer);
    const tx = await swapRouterContract.exactInputSingle(params);
    await tx.wait();
}
```

**Function:**
- **Create Contract Instance:** Initializes the Swap Router contract.
- **Execute Swap:** Calls the `exactInputSingle` method on the Swap Router contract to perform the token swap from USDC to LINK.
- **Waits for Confirmation:** Waits for the swap transaction to be mined and confirmed.

#### `supplyToAave(tokenAddress, amount, signer)`

```javascript
async function supplyToAave(tokenAddress, amount, signer) {
    const aaveContract = new ethers.Contract(AAVE_LENDING_POOL_ADDRESS, AAVE_ABI, signer);
    await approveToken(tokenAddress, LINK_ABI, amount, signer); // Approve Aave to spend LINK
    const tx = await aaveContract.deposit(tokenAddress, amount, signer.address, 0);
    await tx.wait();
}
```

**Function:**
- **Approval:** Calls the `approveToken` function to authorize the Aave Lending Pool to spend LINK tokens.
- **Deposit:** Sends a transaction to deposit LINK tokens into the Aave Lending Pool to earn yield.
- **Waits for Confirmation:** Waits for the deposit transaction to be mined and confirmed.

### 5. Workflow Summary

1. **Approval:** Approves the Swap Router to spend USDC tokens.
2. **Get Pool Info:** Retrieves the Uniswap pool address and details for the token swap.
3. **Prepare Swap Params:** Constructs parameters for the swap transaction.
4. **Execute Swap:** Executes the swap from USDC to LINK using Uniswap.
5. **Supply to Aave:** Approves and deposits LINK tokens into the Aave Lending Pool.

The script automates the process of token swapping and yield farming by interacting with Uniswap and Aave contracts sequentially, ensuring a smooth and efficient workflow.
