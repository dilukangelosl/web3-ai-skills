---
name: hardhat-foundry-testing
description: Smart contract development toolchains and testing strategies. Foundry cheatcodes, Hardhat plugins, fuzz testing, invariant testing, fork testing, gas snapshots, and CI/CD pipelines.
---

# Hardhat & Foundry Testing — Smart Contract Toolchains

Expert knowledge for testing, debugging, and deploying smart contracts with Foundry and Hardhat.

## Use this skill when

- Setting up Foundry or Hardhat projects
- Writing unit, fuzz, or invariant tests
- Fork testing against mainnet state
- Debugging failing transactions
- Setting up CI/CD for Solidity projects
- Comparing Foundry vs Hardhat for a project

---

## Foundry

### Project Commands
```bash
forge init my-project          # New project
forge build                    # Compile
forge test                     # Run tests
forge test -vvvv               # Verbose (show traces)
forge test --match-test "test_Transfer"  # Single test
forge test --gas-report        # Gas usage
forge coverage                 # Code coverage
forge snapshot                 # Gas snapshots
forge fmt                      # Format code
```

### Cheatcodes (vm.)

```solidity
// Identity
vm.prank(alice);             // Next call as alice
vm.startPrank(alice);        // All calls as alice until stopPrank
vm.stopPrank();

// State manipulation
vm.deal(alice, 10 ether);   // Set ETH balance
deal(address(token), alice, 1000e18);  // Set ERC-20 balance
vm.store(addr, slot, value); // Set storage slot

// Time & block
vm.warp(block.timestamp + 1 days);  // Set timestamp
vm.roll(block.number + 100);        // Set block number

// Expectations
vm.expectRevert(MyContract.InsufficientBalance.selector);
vm.expectEmit(true, true, false, true);
emit Transfer(alice, bob, 100);

// Environment
vm.envUint("PRIVATE_KEY");   // Read .env
vm.createSelectFork("mainnet"); // Fork mainnet

// Snapshots
uint256 snap = vm.snapshot();  // Save state
vm.revertTo(snap);             // Restore state

// Labels (for traces)
vm.label(alice, "Alice");
vm.label(address(token), "USDC");
```

### Fuzz Testing
```solidity
function testFuzz_Deposit(uint256 amount) public {
    // Bound inputs to valid range
    amount = bound(amount, 1, MAX_DEPOSIT);

    vm.deal(alice, amount);
    vm.prank(alice);
    vault.deposit{value: amount}();

    assertEq(vault.balanceOf(alice), amount);
}
```

### Invariant Testing
```solidity
// Define target contract and selectors
function setUp() public {
    targetContract(address(vault));
    // Only call these functions during invariant testing
    bytes4[] memory selectors = new bytes4[](2);
    selectors[0] = vault.deposit.selector;
    selectors[1] = vault.withdraw.selector;
    targetSelector(FuzzSelector(address(vault), selectors));
}

function invariant_TotalAssetsMatchDeposits() public {
    assertEq(
        address(vault).balance,
        vault.totalDeposits()
    );
}

function invariant_NoNegativeBalances() public {
    for (uint i = 0; i < actors.length; i++) {
        assertGe(vault.balanceOf(actors[i]), 0);
    }
}
```

### Fork Testing
```solidity
function setUp() public {
    // Fork mainnet at specific block
    vm.createSelectFork("mainnet", 18_000_000);
}

function test_SwapOnUniswap() public {
    // Interact with real mainnet contracts
    IUniswapV2Router02 router = IUniswapV2Router02(UNISWAP_ROUTER);

    vm.deal(alice, 1 ether);
    vm.startPrank(alice);

    address[] memory path = new address[](2);
    path[0] = WETH;
    path[1] = USDC;

    router.swapExactETHForTokens{value: 1 ether}(
        0, path, alice, block.timestamp
    );

    assertGt(IERC20(USDC).balanceOf(alice), 0);
}
```

### Deployment Scripts
```solidity
// script/Deploy.s.sol
contract DeployScript is Script {
    function run() public {
        uint256 key = vm.envUint("PRIVATE_KEY");
        vm.startBroadcast(key);

        MyContract c = new MyContract(arg1, arg2);
        console.log("Deployed:", address(c));

        vm.stopBroadcast();
    }
}
```
```bash
forge script script/Deploy.s.sol \
  --rpc-url $RPC_URL \
  --broadcast \
  --verify \
  --etherscan-api-key $ETHERSCAN_KEY \
  -vvvv
```

---

## Hardhat

### Project Setup
```bash
npx hardhat init                 # New project
npx hardhat compile              # Compile
npx hardhat test                 # Run tests
npx hardhat node                 # Local node
npx hardhat run scripts/deploy.ts --network mainnet
```

### Configuration
```typescript
// hardhat.config.ts
import { HardhatUserConfig } from 'hardhat/config'
import '@nomicfoundation/hardhat-toolbox'

const config: HardhatUserConfig = {
  solidity: {
    version: '0.8.24',
    settings: {
      optimizer: { enabled: true, runs: 200 },
      viaIR: false,
    },
  },
  networks: {
    mainnet: {
      url: process.env.ETH_RPC_URL,
      accounts: [process.env.PRIVATE_KEY!],
    },
    base: {
      url: process.env.BASE_RPC_URL,
      accounts: [process.env.PRIVATE_KEY!],
    },
  },
  etherscan: {
    apiKey: process.env.ETHERSCAN_KEY,
  },
}

export default config
```

### Essential Plugins
| Plugin | Purpose |
|--------|---------|
| `@nomicfoundation/hardhat-toolbox` | All-in-one (ethers, chai, coverage, gas) |
| `@nomicfoundation/hardhat-verify` | Contract verification |
| `hardhat-gas-reporter` | Gas usage per function |
| `hardhat-deploy` | Deterministic deployments |
| `@openzeppelin/hardhat-upgrades` | Proxy deploy + upgrade |

### Testing with Hardhat
```typescript
import { expect } from 'chai'
import { ethers } from 'hardhat'
import { loadFixture } from '@nomicfoundation/hardhat-network-helpers'

describe('MyToken', () => {
  async function deployFixture() {
    const [owner, alice, bob] = await ethers.getSigners()
    const Token = await ethers.getContractFactory('MyToken')
    const token = await Token.deploy('MyToken', 'MTK')
    return { token, owner, alice, bob }
  }

  it('should transfer tokens', async () => {
    const { token, owner, alice } = await loadFixture(deployFixture)
    await token.mint(owner.address, 1000)
    await token.transfer(alice.address, 500)
    expect(await token.balanceOf(alice.address)).to.equal(500)
  })
})
```

---

## CI/CD (GitHub Actions)

```yaml
# .github/workflows/ci.yml
name: Solidity CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive

      - name: Install Foundry
        uses: foundry-rs/foundry-toolchain@v1

      - name: Build
        run: forge build --sizes

      - name: Test
        run: forge test -vvv

      - name: Gas Snapshot
        run: forge snapshot --check

      - name: Coverage
        run: forge coverage --report lcov

      - name: Slither
        uses: crytic/slither-action@v0.4.0
```

---

## Foundry vs Hardhat

| Feature | Foundry | Hardhat |
|---------|---------|---------|
| **Speed** | ⚡ Very fast (Rust) | Slower (Node.js) |
| **Test Language** | Solidity | TypeScript/JS |
| **Fuzz Testing** | ✅ Built-in | ❌ Needs plugins |
| **Invariant Testing** | ✅ Built-in | ❌ |
| **Fork Testing** | ✅ Fast | ✅ Slower |
| **Cheatcodes** | ✅ Extensive | ✅ network-helpers |
| **Plugin Ecosystem** | Growing | Mature |
| **Deployment** | forge script | hardhat-deploy |
| **When to Use** | Performance, Solidity-native | TypeScript ecosystem, plugins |
