---
name: account-abstraction
description: ERC-4337 smart accounts, paymasters, bundlers, session keys, and gas sponsorship. Alchemy AA SDK, Pimlico, ZeroDev, Safe, and EIP-7702.
---

# Account Abstraction — Smart Accounts & Gas Sponsorship

Expert knowledge for implementing ERC-4337 account abstraction in Web3 applications.

## Use this skill when

- Implementing smart contract wallets (not EOAs)
- Sponsoring gas for users (gasless transactions)
- Setting up session keys for limited permissions
- Batching multiple transactions into one
- Building onboarding flows without requiring ETH

## Do not use this skill when

- Connecting external wallets like MetaMask (use `rainbowkit-wagmi`)
- General DApp architecture (use `dapp-patterns`)

---

## Core Concepts

### Architecture
```
User → Signs UserOperation
         ↓
Bundler → Bundles UserOps into single tx
         ↓
EntryPoint Contract → Validates & executes
         ↓
Smart Account → Executes the actual call(s)
         ↓
Paymaster → Sponsors gas (optional)
```

### Key Components
| Component | Role |
|-----------|------|
| **Smart Account** | Contract wallet that holds assets and executes operations |
| **UserOperation** | Pseudo-transaction signed by the user |
| **Bundler** | Service that batches UserOps into on-chain transactions |
| **Paymaster** | Contract that pays gas on behalf of users |
| **EntryPoint** | Singleton contract that validates and executes UserOps |
| **Account Factory** | Contract that deploys new smart accounts |

---

## Alchemy Account Kit (AA SDK)

### Setup
```typescript
import { createModularAccountAlchemyClient } from '@account-kit/smart-contracts'
import { alchemy, sepolia } from '@account-kit/infra'
import { LocalAccountSigner } from '@aa-sdk/core'

const client = await createModularAccountAlchemyClient({
  transport: alchemy({ apiKey: ALCHEMY_API_KEY }),
  chain: sepolia,
  signer: LocalAccountSigner.privateKeyToAccountSigner(PRIVATE_KEY),
  // Gas sponsorship
  gasManagerConfig: {
    policyId: ALCHEMY_GAS_POLICY_ID,
  },
})
```

### Send Sponsored Transaction
```typescript
const { hash } = await client.sendUserOperation({
  uo: {
    target: contractAddress,
    data: encodeFunctionData({
      abi: contractAbi,
      functionName: 'mint',
      args: [1n],
    }),
    value: 0n,
  },
})

// Wait for confirmation
const receipt = await client.waitForUserOperationTransaction({ hash })
```

### Batch Transactions
```typescript
const { hash } = await client.sendUserOperation({
  uo: [
    {
      target: tokenAddress,
      data: encodeFunctionData({
        abi: erc20Abi,
        functionName: 'approve',
        args: [spender, amount],
      }),
    },
    {
      target: spender,
      data: encodeFunctionData({
        abi: spenderAbi,
        functionName: 'deposit',
        args: [amount],
      }),
    },
  ],
})
```

---

## Pimlico (Bundler + Paymaster)

### Setup
```typescript
import { createSmartAccountClient } from 'permissionless'
import { toSimpleSmartAccount } from 'permissionless/accounts'
import { createPimlicoClient } from 'permissionless/clients/pimlico'

// Pimlico bundler + paymaster client
const pimlicoClient = createPimlicoClient({
  transport: http(`https://api.pimlico.io/v2/${chainId}/rpc?apikey=${PIMLICO_KEY}`),
  entryPoint: { address: entryPoint07Address, version: '0.7' },
})

// Smart account
const account = await toSimpleSmartAccount({
  client: publicClient,
  owner: signer,
  entryPoint: { address: entryPoint07Address, version: '0.7' },
})

// Smart account client with gas sponsorship
const smartAccountClient = createSmartAccountClient({
  account,
  chain,
  bundlerTransport: http(`https://api.pimlico.io/v2/${chainId}/rpc?apikey=${PIMLICO_KEY}`),
  paymaster: pimlicoClient,
})
```

### Send Gasless Transaction
```typescript
const hash = await smartAccountClient.sendTransaction({
  to: contractAddress,
  data: calldata,
  value: 0n,
})
```

---

## ZeroDev (Kernel Accounts)

### Setup
```typescript
import { createKernelAccount, createKernelAccountClient } from '@zerodev/sdk'
import { signerToEcdsaValidator } from '@zerodev/ecdsa-validator'

const ecdsaValidator = await signerToEcdsaValidator(publicClient, { signer })

const account = await createKernelAccount(publicClient, {
  plugins: { sudo: ecdsaValidator },
  entryPoint: entryPoint07Address,
})
```

### Session Keys
```typescript
import { signerToSessionKeyValidator } from '@zerodev/session-key'

const sessionKeyValidator = await signerToSessionKeyValidator(publicClient, {
  signer: sessionKeySigner,
  validatorData: {
    permissions: [{
      target: contractAddress,
      functionName: 'transfer',
      args: [{ condition: 'EQUAL', value: recipientAddress }],
      valueLimit: parseEther('0.1'),
    }],
  },
})
```

---

## Safe Smart Accounts

```typescript
import Safe from '@safe-global/protocol-kit'

const protocolKit = await Safe.init({
  provider: rpcUrl,
  signer: privateKey,
  safeAddress: existingSafeAddress,
})

// Create transaction
const safeTransaction = await protocolKit.createTransaction({
  transactions: [{
    to: recipient,
    value: parseEther('0.1').toString(),
    data: '0x',
  }],
})

// Sign and execute
const signedTx = await protocolKit.signTransaction(safeTransaction)
const result = await protocolKit.executeTransaction(signedTx)
```

---

## EIP-7702 (Native AA — Pectra Upgrade)

The next evolution: EOAs can temporarily delegate to smart contract code.

```typescript
// EOA signs an authorization to delegate to a smart contract
const authorization = {
  chainId: 1,
  codeAddress: smartAccountImplementation, // Contract to delegate to
  nonce: await getTransactionCount(client, { address: eoa }),
}

// Send transaction with authorization list
const hash = await sendTransaction(client, {
  authorizationList: [authorization],
  to: targetContract,
  data: calldata,
})
```

### EIP-7702 vs ERC-4337
| Feature | ERC-4337 | EIP-7702 |
|---------|----------|----------|
| **Type** | Smart contract wallet | EOA delegation |
| **Deployment** | New contract per user | No deployment needed |
| **Compatibility** | New address | Same EOA address |
| **Bundler** | Required | Optional |
| **Gas Efficiency** | Higher overhead | Lower overhead |
| **Adoption** | Production-ready | Pectra upgrade |

---

## Provider Selection

| Provider | Smart Accounts | Paymaster | Bundler | Session Keys |
|----------|---------------|-----------|---------|-------------|
| **Alchemy AA** | Modular Account | ✅ Gas Manager | ✅ | ✅ |
| **Pimlico** | Any (Simple, Kernel, Safe) | ✅ Verifying | ✅ | Via account |
| **ZeroDev** | Kernel Account | ✅ | ✅ | ✅ Native |
| **Safe** | Safe Multisig | Via relay | Via relay | ✅ Modules |
| **Biconomy** | Nexus Account | ✅ | ✅ | ✅ |
| **thirdweb** | In-App Wallet | ✅ Built-in | ✅ Built-in | ❌ |
