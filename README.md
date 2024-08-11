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
## Key Functions and Logic

### `approveToken(tokenAddress, tokenABI, amount, wallet)`

**Purpose:** Approves the Swap Router contract to spend a specified amount of USDC tokens.

**Interaction:** Calls the `approve` method of the ERC20 token contract.

### `getPoolInfo(factoryContract, tokenIn, tokenOut)`

**Purpose:** Retrieves the Uniswap pool address for swapping USDC to LINK and fetches pool details.

**Interaction:** Queries the factory contract for the pool address and fetches pool parameters (tokens and fee).

### `prepareSwapParams(poolContract, signer, amountIn)`

**Purpose:** Prepares the parameters required for the token swap transaction.

**Interaction:** Constructs parameters for the `exactInputSingle` method of the Swap Router contract.

### `executeSwap(swapRouter, params, signer)`

**Purpose:** Executes the swap from USDC to LINK.

**Interaction:** Calls the `exactInputSingle` method on the Swap Router contract with prepared parameters.

### `supplyToAave(tokenAddress, amount, signer)`

**Purpose:** Supplies LINK tokens to the Aave Lending Pool for earning yield.

**Interaction:**
1. Approves Aave to spend LINK tokens.
2. Calls the `deposit` method of the Aave Lending Pool contract to deposit the tokens.

### Interaction with DeFi Protocols

- **ERC20 Token Approval:** Interacts with the token contract (e.g., USDC) to allow the Swap Router to spend tokens.
- **Uniswap Pool:** Uses the factory contract to get the pool address and parameters for swapping tokens.
- **Swap Router:** Executes the swap from USDC to LINK using Uniswap’s liquidity pool.
- **Aave Lending Pool:** Approves and deposits LINK tokens into Aave to earn interest.

The script automates token approval, performs a token swap using Uniswap, and deposits the swapped tokens into Aave, handling interactions with these DeFi protocols sequentially.
