## Burn and Mint Cross-Chain NFT

A cross-chain NFT is a smart contract that can exist on any blockchain, abstracting away the need for users to understand which blockchain they’re using.

## Prerequisites

- [Foundry](https://book.getfoundry.sh/getting-started/installation)

## Getting Started

1. Install packages

```
forge install
```

2. Compile contracts

```
forge build
```

3. Create a new file by copying the `.env.example` file, and name it `.env`. Fill in with Ethereum Sepolia and Arbitrum Sepolia RPC URLs using either local archive nodes or a service that provides archival data, like [Infura](https://infura.io/) or [Alchemy](https://alchemy.com/).

```
ETHEREUM_SEPOLIA_RPC_URL=""
ARBITRUM_SEPOLIA_RPC_URL=""
```

4. Run tests

```
forge test
```

5. Deploy on testnets
   On Ethereum Sepolia
   ```
   forge script ./script/Deploy_XNFT.s.sol:DeployXNFT -vvv --broadcast --rpc-url ethereumSepolia
   ```
   On arbitrum Sepolia
   ```
   forge script ./script/Deploy_XNFT_Arbitrum.s.sol:DeployXNFTArbitrum -vvv --broadcast --rpc-url arbitrumSepolia
   ```
6. Call EnableChain Function on Sepolia using the address of the sepolia XNFT contract you deployed.
   ```
   cast send <XNFT_ADDRESS_ON_ETHEREUM_SEPOLIA> --rpc-url ethereumSepolia --private-key=$PRIVATE_KEY "enableChain(uint64,address,bytes)" 3478487238524512106 <XNFT_ADDRESS_ON_ARBITRUM_SEPOLIA> 0x97a657c90000000000000000000000000000000000000000000000000000000000030d40
   ```
7. Call EnableChain Function on Arbitrum using the address of the Arbitrum XNFT contract you deployed.
   ```
   cast send <XNFT_ADDRESS_ON_ARBITRUM_SEPOLIA> --rpc-url arbitrumSepolia --private-key=$PRIVATE_KEY "enableChain(uint64,address,bytes)" 16015286601757825753 <XNFT_ADDRESS_ON_ETHEREUM_SEPOLIA> 0x97a657c90000000000000000000000000000000000000000000000000000000000030d40
   ```
8.Fund the Arbitrum Sepolia contract with few link tokens
9.Mint a New XNFT on Arbitrum Sepolia 
```
cast send <XNFT_ADDRESS_ON_ARBITRUM_SEPOLIA> --rpc-url arbitrumSepolia --private-key=$PRIVATE_KEY "mint()"
```
10. Call crossTransferFrom Function on the same contract paying in LINK tokens.
   ```
cast send <XNFT_ADDRESS_ON_ARBITRUM_SEPOLIA> --rpc-url arbitrumSepolia --private-key=$PRIVATE_KEY "crossChainTransferFrom(address,address,uint256,uint64,uint8)" <YOUR_EOA_ADDRESS> <RECEIVER_ADDRESS> 0 16015286601757825753 1
```
11. Wait for the confirmation on CCIP Explorer and then manually import the NFT from your metamask wallet at sepolia network using the token Address as the XNFT address deployed on Sepolia Chain.
    
## How Do Cross-Chain NFTs Work?

At a high level, an NFT is a digital token on a blockchain with a unique identifier different from any other token on the chain.

Any NFT is implemented by a smart contract that is intrinsically connected to a single blockchain. The smart contract is arguably the most important part of this equation because it controls the NFT implementation: How many are minted, when, what conditions need to be met to distribute them, and more. This means that any cross-chain NFT implementation requires at least two smart contracts on two blockchains and interconnection between them.

This is what a cross-chain NFT looks like - equivalent NFTs that exist across multiple blockchains.

With this in mind, cross-chain NFTs can be implemented in three ways:

- **Burn-and-mint**: An NFT owner puts their NFT into a smart contract on the source chain and burns it, in effect removing it from that blockchain. Once this is done, an equivalent NFT is created on the destination blockchain from its corresponding smart contract. This process can occur in both directions.

- **Lock-and-mint**: An NFT owner locks their NFT into a smart contract on the source chain, and an equivalent NFT is created on the destination blockchain. When the owner wants to move their NFT back, they burn the NFT and it unlocks the NFT on the original blockchain.

- **Lock and unlock**: The same NFT collection is minted on multiple blockchains. An NFT owner can lock their NFT on a source blockchain to unlock the equivalent NFT on a destination blockchain. This means only a single NFT can actively be used at any point in time, even if there are multiple instances of that NFT across blockchains.
