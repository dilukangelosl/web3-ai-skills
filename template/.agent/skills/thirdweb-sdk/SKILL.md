---
name: thirdweb-sdk
description: thirdweb SDK for deploying contracts, connecting wallets (email/passkey/social), gasless transactions, Engine backend, and cross-chain payments.
---

# thirdweb SDK — Unified Web3 Development Platform

Expert knowledge for building with thirdweb's SDK across frontend, backend, and on-chain.

## Use this skill when

- Deploying contracts without writing deploy scripts
- Adding wallet connection with email, passkey, or social login
- Setting up gasless/sponsored transactions
- Using thirdweb Engine for backend wallet management
- Implementing cross-chain payments or token swaps

## Do not use this skill when

- Using RainbowKit for wallet connection (use `rainbowkit-wagmi`)
- Writing raw Solidity patterns (use `solidity-patterns`)

---

## Setup

### Installation
```bash
npm install thirdweb
```

### Client
```typescript
import { createThirdwebClient } from 'thirdweb'

const client = createThirdwebClient({
  clientId: process.env.NEXT_PUBLIC_THIRDWEB_CLIENT_ID!,
})
```

---

## Wallet Connection (Connect)

### ConnectButton (Quick Setup)
```typescript
import { ConnectButton } from 'thirdweb/react'
import { ThirdwebProvider } from 'thirdweb/react'

export default function App() {
  return (
    <ThirdwebProvider>
      <ConnectButton client={client} />
    </ThirdwebProvider>
  )
}
```

### In-App Wallets (Email, Passkey, Social)
```typescript
import { ConnectButton } from 'thirdweb/react'
import { inAppWallet, createWallet } from 'thirdweb/wallets'

const wallets = [
  inAppWallet({
    auth: {
      options: ['email', 'passkey', 'google', 'apple', 'discord'],
    },
    executionMode: {
      mode: 'EIP7702',
      sponsorGas: true, // Gasless for users
    },
  }),
  createWallet('io.metamask'),
  createWallet('com.coinbase.wallet'),
]

<ConnectButton client={client} wallets={wallets} />
```

### ConnectEmbed (Inline)
```typescript
import { ConnectEmbed } from 'thirdweb/react'

// Renders wallet options directly on page (not modal)
<ConnectEmbed client={client} wallets={wallets} />
```

---

## Contract Interaction

### Read Contract
```typescript
import { getContract, readContract } from 'thirdweb'
import { ethereum } from 'thirdweb/chains'

const contract = getContract({
  client,
  chain: ethereum,
  address: '0x...',
})

const totalSupply = await readContract({
  contract,
  method: 'function totalSupply() view returns (uint256)',
  params: [],
})
```

### Write Contract
```typescript
import { prepareContractCall, sendTransaction } from 'thirdweb'
import { useActiveAccount } from 'thirdweb/react'

const account = useActiveAccount()

const transaction = prepareContractCall({
  contract,
  method: 'function mint(address to, uint256 amount)',
  params: [account.address, 100n],
})

const { transactionHash } = await sendTransaction({
  account,
  transaction,
})
```

### Deploy Contract
```bash
# Deploy from CLI
npx thirdweb deploy

# Or programmatically
import { deployContract } from 'thirdweb/deploys'

const address = await deployContract({
  client,
  chain: ethereum,
  account,
  bytecode: '0x...',
  abi: contractAbi,
  constructorParams: [param1, param2],
})
```

---

## React Hooks

```typescript
import {
  useActiveAccount,
  useActiveWallet,
  useReadContract,
  useSendTransaction,
  useWalletBalance,
} from 'thirdweb/react'

// Current account
const account = useActiveAccount()

// Read data
const { data, isLoading } = useReadContract({
  contract,
  method: 'function balanceOf(address) view returns (uint256)',
  params: [account?.address],
})

// Send transaction
const { mutate: sendTx, isPending } = useSendTransaction()
sendTx(transaction)

// Wallet balance
const { data: balance } = useWalletBalance({
  client,
  chain: ethereum,
  address: account?.address,
})
```

---

## thirdweb Engine (Backend)

Server-side wallet management and transaction processing.

```typescript
// Engine is a self-hosted HTTP server
// POST /contract/:chain/:address/write

const response = await fetch(
  `${ENGINE_URL}/contract/${chainId}/${contractAddress}/write`,
  {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${ENGINE_ACCESS_TOKEN}`,
      'x-backend-wallet-address': backendWalletAddress,
    },
    body: JSON.stringify({
      functionName: 'transfer',
      args: [toAddress, amount],
    }),
  }
)
```

### Engine Features
| Feature | Description |
|---------|-------------|
| **Managed Wallets** | Create and manage wallets server-side |
| **Nonce Management** | Automatic nonce handling for parallel tx |
| **Gas Management** | Auto gas estimation and retries |
| **Webhooks** | Transaction status notifications |
| **Idempotent Tx** | Prevent duplicate transactions |

---

## Pay (Onramp + Bridge)

```typescript
import { PayEmbed } from 'thirdweb/react'

// Fiat → Crypto onramp + cross-chain bridge
<PayEmbed client={client} />
```

---

## thirdweb vs RainbowKit

| Feature | thirdweb | RainbowKit |
|---------|----------|------------|
| **Wallet UI** | ConnectButton/Embed | ConnectButton |
| **In-App Wallets** | ✅ Email, passkey, social | ❌ External only |
| **Account Abstraction** | ✅ Built-in | ❌ Separate |
| **Contract Deploy** | ✅ CLI + SDK | ❌ |
| **Backend Engine** | ✅ | ❌ |
| **Customization** | Moderate | High |
| **Dependency** | thirdweb only | Wagmi + viem |
