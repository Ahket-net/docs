# AHK Token

## Table of Contents <!-- omit from toc -->
- [AHK Token](#markdown-header-ahk-token)
   - [AHK Token Overview](#markdown-header-ahk-token-overview)
   - [Interaction with STASH DBN Treasury](#markdown-header-interaction-with-stash-dbn-treasury)
      - [Example of AHK Token Utility](#markdown-header-example-of-ahk-token-utility)
   - [Smart Contract Implementation](#markdown-header-smart-contract-implementation)
      - [AHK Token Smart Contract (ERC20)](#markdown-header-ahk-token-smart-contract-erc20)
      - [SMASH Treasury Smart Contract](#markdown-header-smash-treasury-smart-contract)
      - [AHK Vesting Contract](#markdown-header-ahk-vesting-contract)

## AHK Token Overview

- Related documents:
   - STASH Reference Manual
      - STASH DBN Treasury

The AHK token is an integral component of the Ahket ecosystem, specifically designed to represent ownership shares in the STASH DBN Treasury. The initial minting of AHK tokens is set at 100 million. At the outset, the STASH DBN Treasury will not contain any DBN, rendering the initial value of each AHK token as zero. However, as the utilization of STASH and Ahket's verification services increases, DBN will begin to accumulate in the STASH DBN Treasury, thereby enhancing the value of AHK tokens.

## Interaction with STASH DBN Treasury

AHK token holders have the unique ability to burn their tokens to withdraw their proportional share of DBN from the STASH DBN Treasury. This process not only allows token holders to extract value directly from the treasury but also reduces the total supply of AHK tokens in circulation, potentially increasing the value of the remaining tokens.

### Example of AHK Token Utility

Consider a scenario where the total supply of AHK tokens is 100 million. If an AHK holder decides to burn 1.5 AHK, they will receive a corresponding share of the DBN stored in the STASH DBN Treasury, calculated as 1.5/100,000,000 of the treasury's total DBN. Concurrently, the total supply of AHK tokens will adjust to 99,999,998.5, reflecting the burnt tokens.

## Smart Contract Implementation

The mechanism for burning AHK tokens and extracting DBN from the STASH DBN Treasury is governed by smart contracts. These contracts ensure the seamless and secure execution of these operations, providing a transparent and efficient process for token holders. Through the use of smart contracts, Ahket guarantees the integrity of the AHK token's functionality and the equitable distribution of the STASH DBN Treasury's assets.

### AHK Token Smart Contract (ERC20)

This is a standard ERC20 smart contract. A total of 100 million AHKs will be minted at construction.

### SMASH Treasury Smart Contract

The STASH DBN Treasury smart contract manages the DBN assets collected from the utilization of STASH and verification services. It also handles the distribution of DBN to AHK token holders who burn their tokens. Holders of AHK tokens can interact directly with this smart contract to burn amounts of AHK tokens and receive DBN in return.

### AHK Vesting Contract

This contract designed to foster long-term engagement and reward contributions. This smart contract is tailored for specific users, such as developers, by locking AHK tokens and releasing them according to a personalized schedule. Upon locking their AHK tokens, users are granted an equivalent amount of lAHK (locked AHK), symbolizing their vested interest. This contract not only incentivizes sustained participation but also aligns user contributions with the growth and success of the Ahket platform. Through the AHK Vesting Contract, Ahket ensures a committed partnership with its key contributors while maintaining the integrity and value of the AHK token within its ecosystem.