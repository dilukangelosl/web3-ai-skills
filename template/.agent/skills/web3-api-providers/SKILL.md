---
name: web3-api-providers
description: Enhanced APIs from Alchemy, Moralis, Infura, and QuickNode. NFT APIs, Token APIs, Webhooks, Streams, and blockchain data services for production DApps.
---

# Web3 API Providers — Enhanced Blockchain Data Services

Expert knowledge for leveraging Web3 API provider SDKs and enhanced APIs beyond basic RPC.

## Use this skill when

- Fetching NFT data (metadata, ownership, transfers)
- Querying token balances and transfer history
- Setting up webhooks for real-time blockchain events
- Using enhanced APIs (asset transfers, trace calls)
- Choosing between Alchemy, Moralis, Infura, or QuickNode

## Do not use this skill when

- Optimizing raw RPC calls or Multicall3 (use `rpc-optimization`)
- Building subgraphs or indexers (use `subgraph-indexing`)

---

## Alchemy SDK

### Setup
```typescript
import { Alchemy, Network } from 'alchemy-sdk'

const alchemy = new Alchemy({
  apiKey: process.env.ALCHEMY_API_KEY,
  network: Network.ETH_MAINNET,
})
```

### NFT API
```typescript
// Get all NFTs for an owner
const nfts = await alchemy.nft.getNftsForOwner('0x...')

// Get NFT metadata
const metadata = await alchemy.nft.getNftMetadata(contractAddress, tokenId)

// Get owners of an NFT collection
const owners = await alchemy.nft.getOwnersForContract(contractAddress)

// Get NFTs for a contract
const contractNfts = await alchemy.nft.getNftsForContract(contractAddress)

// Verify NFT ownership
const isOwner = await alchemy.nft.verifyNftOwnership(ownerAddress, contractAddress)

// Get floor price
const floorPrice = await alchemy.nft.getFloorPrice(contractAddress)
```

### Token API
```typescript
// Get all token balances
const balances = await alchemy.core.getTokenBalances(ownerAddress)

// Get token metadata (name, symbol, decimals, logo)
const metadata = await alchemy.core.getTokenMetadata(tokenAddress)

// Get asset transfers (ERC-20, ERC-721, ERC-1155, native)
const transfers = await alchemy.core.getAssetTransfers({
  fromBlock: '0x0',
  toBlock: 'latest',
  toAddress: '0x...',
  category: ['erc20', 'erc721', 'external'],
})
```

### Webhooks (Alchemy Notify)
```typescript
// Create address activity webhook
const webhook = await alchemy.notify.createWebhook({
  url: 'https://your-server.com/webhook',
  type: 'ADDRESS_ACTIVITY',
  addresses: ['0x...'],
})

// Mined transaction webhook
const minedWebhook = await alchemy.notify.createWebhook({
  url: 'https://your-server.com/webhook',
  type: 'MINED_TRANSACTION',
  addresses: ['0x...'],
})
```

### Enhanced WebSockets
```typescript
// Subscribe to pending transactions
alchemy.ws.on({
  method: 'alchemy_pendingTransactions',
  toAddress: contractAddress,
}, (tx) => {
  console.log('Pending tx:', tx.hash)
})

// Subscribe to log events
alchemy.ws.on({
  method: 'eth_subscribe',
  params: ['logs', { address: contractAddress, topics: [] }],
}, (log) => {
  console.log('New log:', log)
})
```

---

## Moralis SDK

### Setup
```typescript
import Moralis from 'moralis'

await Moralis.start({ apiKey: process.env.MORALIS_API_KEY })
```

### NFT API
```typescript
// Get NFTs by wallet
const nfts = await Moralis.EvmApi.nft.getWalletNFTs({
  address: '0x...',
  chain: '0x1', // Ethereum
})

// Get NFT metadata
const metadata = await Moralis.EvmApi.nft.getNFTMetadata({
  address: contractAddress,
  tokenId: '1',
  chain: '0x1',
})

// Get NFT transfers
const transfers = await Moralis.EvmApi.nft.getNFTTransfers({
  address: contractAddress,
  tokenId: '1',
  chain: '0x1',
})
```

### Token API
```typescript
// Get ERC-20 balances
const balances = await Moralis.EvmApi.token.getWalletTokenBalances({
  address: '0x...',
  chain: '0x1',
})

// Get token price
const price = await Moralis.EvmApi.token.getTokenPrice({
  address: tokenAddress,
  chain: '0x1',
})

// Get token transfers
const transfers = await Moralis.EvmApi.token.getWalletTokenTransfers({
  address: '0x...',
  chain: '0x1',
})
```

### Streams API (Real-Time Webhooks)
```typescript
// Create a stream for contract events
const stream = await Moralis.Streams.add({
  chains: ['0x1'],
  tag: 'my-stream',
  description: 'Track USDC transfers',
  webhookUrl: 'https://your-server.com/moralis-webhook',
  includeContractLogs: true,
  abi: usdcAbi,
  topic0: ['Transfer(address,address,uint256)'],
})

// Add address to stream
await Moralis.Streams.addAddress({
  id: stream.result.id,
  address: usdcAddress,
})
```

### DeFi API
```typescript
// Get DeFi positions
const positions = await Moralis.EvmApi.defi.getDefiPositionsByProtocol({
  address: '0x...',
  chain: '0x1',
  protocol: 'uniswap-v3',
})
```

---

## Infura

### Setup
```typescript
import { InfuraProvider } from 'ethers'

const provider = new InfuraProvider('mainnet', process.env.INFURA_PROJECT_ID)
```

### IPFS Gateway
```typescript
// Upload to IPFS via Infura
const response = await fetch('https://ipfs.infura.io:5001/api/v0/add', {
  method: 'POST',
  headers: {
    Authorization: `Basic ${Buffer.from(`${PROJECT_ID}:${PROJECT_SECRET}`).toString('base64')}`,
  },
  body: formData,
})
```

### MetaMask SDK Integration
```typescript
import { MetaMaskSDK } from '@metamask/sdk'

const MMSDK = new MetaMaskSDK({
  infuraAPIKey: process.env.INFURA_API_KEY,
  dappMetadata: {
    name: 'My DApp',
    url: window.location.href,
  },
})

const ethereum = MMSDK.getProvider()
```

---

## QuickNode

### Streams (Real-time)
```typescript
// QuickNode Streams — configured via dashboard or API
// Delivers filtered blockchain data to your endpoint

// Stream filter example (QN API):
const filter = {
  network: 'ethereum-mainnet',
  expression: `
    tx_logs_address == '${contractAddress}' &&
    tx_logs_topic0 == '${transferTopic}'
  `,
  destination: 'https://your-server.com/qn-webhook',
}
```

### Functions (Serverless)
```javascript
// QuickNode Functions — serverless JS at the edge
// Triggered by blockchain events or HTTP requests
export async function main(params) {
  const { data } = params
  // Process blockchain data
  return { statusCode: 200, body: JSON.stringify(result) }
}
```

---

## Provider Selection Guide

| Provider | Best For | NFT API | Token API | Webhooks | Free Tier |
|----------|----------|---------|-----------|----------|-----------|
| **Alchemy** | Full-stack DApps | ✅ Rich | ✅ Enhanced | ✅ Notify | 300M CU/mo |
| **Moralis** | Rapid prototyping | ✅ Rich | ✅ + Prices | ✅ Streams | 40K CU/day |
| **Infura** | MetaMask ecosystem | ❌ Basic | ❌ Basic | ❌ | 100K req/day |
| **QuickNode** | High-perf infra | ✅ Addon | ✅ Addon | ✅ Streams | 10M credits |

### When to Use What
- **Need NFT/Token data fast?** → Alchemy or Moralis
- **Need real-time webhooks?** → Moralis Streams or Alchemy Notify
- **Need MetaMask integration?** → Infura
- **Need custom streaming + functions?** → QuickNode
- **Need DeFi positions?** → Moralis
