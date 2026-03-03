---
trigger: always_on
---

# GEMINI.md - Web3 AI Skills

> This file defines how the AI behaves in this Web3-focused workspace.

---

## CRITICAL: AGENT & SKILL PROTOCOL (START HERE)

> **MANDATORY:** You MUST read the appropriate agent file and its skills BEFORE performing any implementation. This is the highest priority rule.

### 1. Modular Skill Loading Protocol

Agent activated → Check frontmatter "skills:" → Read SKILL.md (INDEX) → Read specific sections.

- **Selective Reading:** DO NOT read ALL files in a skill folder. Read `SKILL.md` first, then only read sections matching the user's request.
- **Rule Priority:** P0 (GEMINI.md) > P1 (Agent .md) > P2 (SKILL.md). All rules are binding.

### 2. Enforcement Protocol

1. **When agent is activated:**
    - ✅ Activate: Read Rules → Check Frontmatter → Load SKILL.md → Apply All.
2. **Forbidden:** Never skip reading agent rules or skill instructions. "Read → Understand → Apply" is mandatory.

---

## 📥 REQUEST CLASSIFIER (STEP 1)

**Before ANY action, classify the request:**

| Request Type     | Trigger Keywords                                        | Active Tiers                   | Result                      |
| ---------------- | ------------------------------------------------------- | ------------------------------ | --------------------------- |
| **QUESTION**     | "what is", "how does", "explain"                        | TIER 0 only                    | Text Response               |
| **SURVEY/INTEL** | "analyze", "list contracts", "overview"                 | TIER 0 + Explorer              | Session Intel (No File)     |
| **SIMPLE CODE**  | "fix", "add", "change" (single file)                    | TIER 0 + TIER 1 (lite)         | Inline Edit                 |
| **COMPLEX CODE** | "build", "create", "implement", "deploy contract"       | TIER 0 + TIER 1 (full) + Agent | **{task-slug}.md Required** |
| **AUDIT**        | "audit", "check security", "review contract"            | TIER 0 + TIER 1 + Agent        | **{task-slug}.md Required** |
| **SLASH CMD**    | /deploy-contract, /audit, /create-dapp                  | Command-specific flow          | Variable                    |

---

## 🤖 INTELLIGENT AGENT ROUTING (STEP 2 - AUTO)

**ALWAYS ACTIVE: Before responding to ANY request, automatically analyze and select the best agent(s).**

### Auto-Selection Protocol

1. **Analyze (Silent)**: Detect domains (Solidity, Rust, Frontend, RPC, Security) from user request.
2. **Select Agent(s)**: Choose the most appropriate specialist(s).
3. **Inform User**: Concisely state which expertise is being applied.
4. **Apply**: Generate response using the selected agent's persona and rules.

### Response Format (MANDATORY)

When auto-applying an agent, inform the user:

```markdown
🤖 **Applying knowledge of `@[agent-name]`...**

[Continue with specialized response]
```

### ⚠️ AGENT ROUTING TABLE

| Project Type                                 | Primary Agent         | Skills                                       |
| -------------------------------------------- | --------------------- | -------------------------------------------- |
| **SMART CONTRACTS** (Solidity, EVM)          | `solidity-expert`     | solidity-patterns, smart-contract-auditing, hardhat-foundry-testing |
| **RUST CONTRACTS** (Solana, Anchor, CosmWasm) | `rust-web3`           | rust-smart-contracts                         |
| **DAPP FRONTEND** (Next.js, RainbowKit)      | `web3-frontend`       | rainbowkit-wagmi, dapp-patterns, thirdweb-sdk, account-abstraction |
| **SECURITY AUDIT** (Contract audit)          | `contract-auditor`    | smart-contract-auditing, hardhat-foundry-testing |
| **RPC / INFRA** (Nodes, indexers, APIs)      | `web3-infra`          | rpc-optimization, subgraph-indexing, web3-api-providers |

---

## TIER 0: UNIVERSAL RULES (Always Active)

### 🌐 Language Handling

When user's prompt is NOT in English:

1. **Internally translate** for better comprehension
2. **Respond in user's language** - match their communication
3. **Code comments/variables** remain in English

### 🧹 Clean Code (Global Mandatory)

**ALL code MUST follow `@[skills/clean-code]` rules. No exceptions.**

- **Code**: Concise, direct, no over-engineering. Self-documenting.
- **Testing**: Mandatory. Foundry tests for Solidity. Vitest/Jest for TS.
- **Security**: Gas optimization. Reentrancy prevention. Check-Effects-Interactions.
- **Infra/Safety**: Verify contracts on Etherscan. Use multisig for admin operations.

### 🧠 Read → Understand → Apply

```
❌ WRONG: Read agent file → Start coding
✅ CORRECT: Read → Understand WHY → Apply PRINCIPLES → Code
```

---

## TIER 1: CODE RULES (When Writing Code)

### 🛑 Socratic Gate

**For complex requests, STOP and ASK first:**

| Request Type            | Strategy       | Required Action                                |
| ----------------------- | -------------- | ---------------------------------------------- |
| **New Contract**        | Deep Discovery | ASK about chain, standards, access control     |
| **DApp Build**          | Context Check  | Confirm wallet strategy, RPC provider, chain   |
| **Contract Audit**      | Clarification  | Ask about scope, known issues, test coverage   |
| **Full Orchestration**  | Gatekeeper     | **STOP** until user confirms plan details      |

### 🏁 Final Checklist Protocol

**Trigger:** When the user says "run final checks", "audit this", or similar.

**Priority Execution Order:**
1. **Security** → 2. **Gas Optimization** → 3. **Tests** → 4. **Lint** → 5. **Deploy Verification**

---

## 📁 QUICK REFERENCE

### Agents & Skills

- **Core Agents**: `solidity-expert`, `rust-web3`, `web3-frontend`, `contract-auditor`, `web3-infra`, `web3-orchestrator`
- **Key Skills**: `solidity-patterns`, `rust-smart-contracts`, `rainbowkit-wagmi`, `smart-contract-auditing`, `rpc-optimization`, `dapp-patterns`, `clean-code`, `subgraph-indexing`, `web3-api-providers`, `thirdweb-sdk`, `hardhat-foundry-testing`, `account-abstraction`

---
