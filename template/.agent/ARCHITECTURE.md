# Web3 AI Skills Architecture

> Comprehensive Web3 Agent Capability Expansion Toolkit — 2026 Edition

---

## 📋 Overview

Web3 AI Skills is a modular system consisting of:

- **6 Specialist Agents** — Web3-specific role-based AI personas
- **12 Skills** — Deep domain knowledge modules for blockchain development
- **4 Workflows** — Slash command procedures for common Web3 tasks

---

## 🏗️ Directory Structure

```plaintext
.agent/
├── GEMINI.md                # Core instructions
├── ARCHITECTURE.md          # This file
├── agents/                  # 6 Specialist Agents
├── skills/                  # 12 Skills
├── workflows/               # 4 Slash Commands
└── scripts/                 # Validation Scripts
```

---

## 🤖 Agents (6)

| Agent                  | Focus                             | Skills Used                                      |
| ---------------------- | --------------------------------- | ------------------------------------------------ |
| `solidity-expert`      | EVM smart contracts, Foundry      | solidity-patterns, smart-contract-auditing, hardhat-foundry-testing |
| `rust-web3`            | Solana/Anchor, CosmWasm, Rust     | rust-smart-contracts                              |
| `web3-frontend`        | Next.js + RainbowKit + Wagmi      | rainbowkit-wagmi, dapp-patterns, thirdweb-sdk, account-abstraction |
| `contract-auditor`     | Security audits, Slither, Mythril | smart-contract-auditing, hardhat-foundry-testing  |
| `web3-infra`           | RPC, indexers, nodes, subgraphs   | rpc-optimization, subgraph-indexing, web3-api-providers |
| `web3-orchestrator`    | Multi-agent Web3 coordination     | All skills                                        |

---

## 🧩 Skills (12)

### Smart Contracts

| Skill                       | Description                                        |
| --------------------------- | -------------------------------------------------- |
| `solidity-patterns`         | Solidity 0.8.x+, ERC standards, gas optimization  |
| `rust-smart-contracts`      | Anchor/Solana programs, CosmWasm, Stylus           |
| `smart-contract-auditing`   | Audit methodology, Slither, Mythril, Aderyn        |
| `hardhat-foundry-testing`   | Toolchains, cheatcodes, fuzz/invariant testing, CI |

### DApp Frontend

| Skill                       | Description                                        |
| --------------------------- | -------------------------------------------------- |
| `rainbowkit-wagmi`          | RainbowKit v2, Wagmi v2, viem, wallet integration |
| `dapp-patterns`             | DApp architecture, IPFS, ENS, signing patterns     |
| `thirdweb-sdk`              | Contract deploy, in-app wallets, Engine, Pay       |
| `account-abstraction`       | ERC-4337, paymasters, session keys, EIP-7702       |

### Infrastructure

| Skill                       | Description                                        |
| --------------------------- | -------------------------------------------------- |
| `rpc-optimization`          | RPC management, Multicall3, batching, MEV          |
| `subgraph-indexing`         | The Graph, Ponder, Subsquid, Envio, event parsing  |
| `web3-api-providers`        | Alchemy, Moralis, Infura, QuickNode enhanced APIs  |

### General

| Skill                       | Description                                        |
| --------------------------- | -------------------------------------------------- |
| `clean-code`                | Coding standards (Global) applied to Web3          |

---

## 🔄 Workflows (4)

| Command            | Description                                  |
| ------------------ | -------------------------------------------- |
| `/deploy-contract` | Deploy & verify smart contracts              |
| `/audit`           | Run security audit on smart contracts        |
| `/create-dapp`     | Scaffold a new DApp with wallet integration  |
| `/create-contract` | Scaffold a new smart contract project        |

---

## 🎯 Skill Loading Protocol

```plaintext
User Request → Skill Description Match → Load SKILL.md
                                            ↓
                                    Read references/
                                            ↓
                                    Read scripts/
```

### Skill Structure

```plaintext
skill-name/
├── SKILL.md           # (Required) Metadata & instructions
├── scripts/           # (Optional) Python/Bash scripts
├── references/        # (Optional) Templates, docs
└── assets/            # (Optional) Images, logos
```

---

## 📊 Statistics

| Metric              | Value        |
| ------------------- | ------------ |
| **Total Agents**    | 6            |
| **Total Skills**    | 12           |
| **Total Workflows** | 4            |
| **Coverage**        | Web3 / EVM / Solana / DApp / AA / APIs |

---

## 🔗 Quick Reference

| Need               | Agent                | Skills                                       |
| ------------------- | -------------------- | -------------------------------------------- |
| Solidity Contract   | `solidity-expert`    | solidity-patterns, hardhat-foundry-testing    |
| Rust Contract       | `rust-web3`          | rust-smart-contracts                          |
| DApp Frontend       | `web3-frontend`      | rainbowkit-wagmi, dapp-patterns, thirdweb-sdk |
| Contract Audit      | `contract-auditor`   | smart-contract-auditing, hardhat-foundry-testing |
| RPC / Infra         | `web3-infra`         | rpc-optimization, subgraph-indexing, web3-api-providers |
| Account Abstraction | `web3-frontend`      | account-abstraction, thirdweb-sdk            |
| Full-Stack DApp     | `web3-orchestrator`  | All skills                                    |
