<div align="center">

<img src="https://img.shields.io/badge/Polkadot%20Hub-EVM-E6007A?style=for-the-badge&logo=polkadot&logoColor=white"/>
<img src="https://img.shields.io/badge/OpenZeppelin-4.x-4E5EE4?style=for-the-badge&logo=openzeppelin&logoColor=white"/>
<img src="https://img.shields.io/badge/Solidity-0.8.x-363636?style=for-the-badge&logo=solidity&logoColor=white"/>
<img src="https://img.shields.io/badge/Foundry-Deployed-orange?style=for-the-badge"/>

<br/><br/>

# 🧠 Agentra
### *The Decentralized AI Agent Economy — Built on Polkadot Hub*

**A permissionless infrastructure protocol that lets developers monetize AI agents on-chain and gives users a single, trustless gateway to access them.**

<br/>

[🎬 Watch Demo](https://canva.link/uxmz5s79nca4jxi) &nbsp;·&nbsp; [📦 GitHub Repo](https://github.com/iammohit64/agentra-polkadot) &nbsp;·&nbsp; [🔗 Marketplace Contract](https://polkadot.testnet.routescan.io/address/0x2471aDE757f7a2D93d928D2C115EB4D0F176edE3) &nbsp;·&nbsp; [🪙 AGT Token](https://polkadot.testnet.routescan.io/address/0x52d47a587382d98B51fe645a2F62C0203E37B175)

</div>

---

## The Real Problem Nobody Is Solving

There are thousands of developers right now building genuinely brilliant AI workflows — automating research, coding, customer support, data pipelines — using tools like LangChain, AutoGen, and MCP. They share them on GitHub, maybe tweet about them, and then... nothing. No way to charge for them. No distribution. No trust layer.

Meanwhile, users who want to access these agents are scattered across 10 different SaaS dashboards with 10 different subscriptions, zero guarantees about uptime or quality, and no way to verify if an agent actually does what it claims.

This is a coordination failure. And it's fixable.

The existing "solutions" — OpenAI's GPT Store, Hugging Face Spaces — are walled gardens. The developer doesn't control pricing. The platform takes the lion's share. Users are locked in. There's no transparency on performance, and there's certainly no on-chain accountability.

**Agentra is the open alternative.** Not just a marketplace — an infrastructure layer for the AI agent economy.

---

## What Agentra Actually Is

Think of it like this: Agentra does for AI agents what Uniswap did for token trading — removes the middleman, puts the logic on-chain, and makes the rules transparent and enforced by code, not by a company's terms of service.

**Developers** deploy their agent's metadata, MCP (Model Context Protocol) schema, and pricing to the Agentra smart contract. They set a price in `$AGT` tokens. That's it. The contract handles access control, subscription verification, and payment routing automatically.

**Users** come to one unified dashboard, discover agents ranked by real on-chain upvotes and verified execution metrics, buy access trustlessly through a smart contract interaction, and manage every AI subscription they have from a single Web3 interface.

No credit card forms. No "contact sales." No wondering if the developer will keep the lights on. The contract is the guarantee.

---

## 🏆 Hackathon Track Submissions

### Track 1 — EVM Smart Contract (AI-Powered dApps)

Agentra directly addresses the AI-powered dApps category by creating a production-deployed, end-to-end marketplace where off-chain AI agent execution (via MCP schemas) is governed by on-chain smart contract access control — all running on Polkadot Hub.

This isn't a wrapper around an existing idea. We built a real bridge between decentralized compute infrastructure and AI tooling, leveraging Polkadot Hub's low fees and EVM compatibility to make on-chain AI access economically viable for the first time.

---

### OpenZeppelin Sponsor Track

We didn't touch `ERC20` and call it a day. Our entire contract architecture is built on composing OpenZeppelin's production-grade primitives to solve real problems in a live financial protocol.

Here's what we used and — more importantly — why:

**`SafeERC20` — because broken tokens are everywhere**

The ERC20 standard has a dirty secret: not every token actually returns a boolean on `transfer` and `transferFrom`. In a marketplace where users are sending real tokens for agent access, a silent failure could mean a user loses funds without getting access, or worse, an attacker exploits the inconsistency. We wrapped every token interaction in `SafeERC20` so that doesn't happen. Ever.

**`AccessControl` over `Ownable` — because we're building for teams, not solo deployers**

The standard `Ownable` pattern makes one wallet the god of the entire contract. That's fine for a toy project. For a protocol handling user funds, it's a single point of failure. We defined three distinct roles:

- `DEFAULT_ADMIN_ROLE` — emergency protocol pauses only. This key should be in cold storage.
- `FEE_MANAGER_ROLE` — adjusts tier pricing and listing fees. Operational, day-to-day.
- `MINTER_ROLE` — scoped exclusively to the token contract. Can't touch the marketplace logic.

This is how real protocols operate. It means a compromised operational key can't drain the treasury, and a compromised admin key can't manipulate fee structures.

**`Pausable` — because AI is still experimental**

If a critical bug surfaces in agent execution routing, we need to freeze the protocol without a full migration. OpenZeppelin's `Pausable` modifier wraps `deployAgent`, `purchaseAccess`, and `upvote` — so in an emergency, a single transaction from the admin wallet stops all user fund movement. This isn't pessimism; it's responsible product design.

**`ERC20Permit` + `ERC20Burnable` on `$AGT`**

`ERC20Permit` (EIP-2612) enables future gasless approval flows — users can sign off-chain and let a relayer submit the transaction, improving UX dramatically as we scale. `ERC20Burnable` lets us implement deflationary tokenomics down the road — platform fees can be burned, creating a direct link between protocol usage and token value.

---

## ⚙️ Technical Architecture

### The Automated Deployment Pipeline

This is something we're genuinely proud of and it's more than a developer convenience — it's a demonstration of production-level thinking.

When we deploy contracts with Foundry, a single post-deploy script (`format_deployments.js`) automatically reads the broadcast artifacts and syncs the verified contract addresses and compact ABIs directly into the frontend. Zero manual copy-paste. Zero risk of committing a wrong address.

```bash
forge script script/Deploy.s.sol:DeployScript \
  --rpc-url https://services.polkadothub-rpc.com/testnet \
  --broadcast --legacy

# Frontend is automatically synced:
node format_deployments.js 420420417
# ✅ Deployments auto-synced to frontend for Chain ID 420420417 (with compact ABIs)
```

This is how professional EVM teams ship. The frontend always reflects the canonical on-chain state — not a developer's clipboard.

---

### The Draft → Active State Machine

This solves a real UX problem nobody talks about: what happens when a user clicks "Deploy Agent," the transaction gets submitted, but they reject MetaMask? Without handling this, you get "ghost agents" — dead entries in your database pointing to contracts that never confirmed.

Our backend creates agents in a `Draft` state immediately. They only transition to `Active` when the frontend passes a confirmed `txHash` back to the state machine. If the transaction never confirms, the draft expires cleanly. Users only see real, live agents.

---

### The Network Enforcer

If a user connects a wallet on the wrong chain (Ethereum mainnet, a testnet, anything that isn't Polkadot Hub), the frontend traps the entire UI and forces a network switch before any interaction is allowed. Not a warning. Not a toast notification. A hard gate.

This prevents the single most common source of "I lost my tokens" support tickets in Web3 products.

---

### Batch Multicall Dashboard

When a user opens their dashboard to see which agents they have access to, we don't make 20 separate RPC calls. We use Wagmi's `useReadContracts` to batch every access verification into a single multicall RPC request. This dramatically reduces RPC load on Polkadot Hub and makes the dashboard feel instant even as the marketplace scales to hundreds of agents.

---

### On-Chain Upvoting as an Incentive Loop

1 `$AGT` = 1 Upvote. When a user upvotes an agent, the token goes **directly to the agent creator's wallet** — not to a platform pool, not to a DAO treasury. Straight to the developer.

This creates a direct financial incentive for creators to build agents worth upvoting, and a discovery mechanism that's harder to game than star ratings because it costs real money to manipulate.

---

## 🌍 Live Deployments

Deployed on **Polkadot Hub Testnet** (Chain ID: `420420417`)

| Contract | Address |
|---|---|
| **AgentToken ($AGT)** | [`0x52d47a587382d98B51fe645a2F62C0203E37B175`](https://polkadot.testnet.routescan.io/address/0x52d47a587382d98B51fe645a2F62C0203E37B175) |
| **Agentra Marketplace** | [`0x2471aDE757f7a2D93d928D2C115EB4D0F176edE3`](https://polkadot.testnet.routescan.io/address/0x2471aDE757f7a2D93d928D2C115EB4D0F176edE3) |

> Both contracts deployed in a single Foundry broadcast, verified on-chain, and automatically synced to the frontend in under 30 seconds.

---

## 💰 Business Model — How Agentra Sustains Itself

This isn't a hackathon demo that shuts down on March 21st. The protocol is designed to be self-sustaining from day one.

**Listing Fees (Upfront Revenue)**
Developers pay an `$AGT` fee to list an agent, tiered by plan: Standard, Professional, or Enterprise. This creates immediate protocol revenue and filters out low-quality spam listings.

**Platform Cut (Recurring Revenue)**
The protocol takes a flat 20% of every agent access purchase, routed directly to the `feeCollector` wallet via the smart contract. No invoices. No payment processing delays. Every subscription triggers an on-chain split.

**Creator Economy (Retention Flywheel)**
100% of upvote revenue goes to creators. This isn't charity — it's strategic. High-earning creators market themselves, which markets Agentra, which markets Polkadot Hub. Everyone wins.

As the ecosystem grows and `$AGT` accrues utility (governance, staking, deflationary burns), the token flywheel reinforces every part of this model.

---

## 🛠️ Tech Stack

| Layer | Stack |
|---|---|
| **Blockchain** | Polkadot Hub Testnet (EVM, Chain ID: 420420417) |
| **Smart Contracts** | Solidity, Foundry, OpenZeppelin |
| **Frontend** | React, Vite, TailwindCSS, Wagmi v2, Viem, Web3Modal |
| **Backend** | Node.js, Express, Prisma, MongoDB |
| **Deployment** | Automated via Foundry scripts + `format_deployments.js` |

---

## 🚀 Run It Locally

```bash
# Clone the repo
git clone https://github.com/iammohit64/agentra-polkadot.git
cd agentra-polkadot

# Install frontend dependencies
cd frontend && npm install

# Configure environment
cp .env.example .env
# Add your WalletConnect Project ID and RPC URL

# Start the frontend
npm run dev

# In a separate terminal — start the backend
cd ../backend && npm install
npm run dev
```

For smart contract deployment:

```bash
cd contracts
forge build
forge script script/Deploy.s.sol:DeployScript \
  --rpc-url https://services.polkadothub-rpc.com/testnet \
  --broadcast --legacy

# Sync addresses to frontend automatically
node format_deployments.js 420420417
```

---

## 📐 Contract Architecture

```
contracts/
├── src/
│   ├── AgentToken.sol         # $AGT — ERC20Permit + ERC20Burnable + AccessControl
│   └── AgentraMarketplace.sol # Core protocol — SafeERC20, Pausable, AccessControl
├── script/
│   └── Deploy.s.sol           # Foundry deployment script (both contracts, one broadcast)
└── format_deployments.js      # Auto-syncs ABIs + addresses → frontend config
```

---

## 🔮 Where This Goes Next

The contracts are deployed. The pipeline is automated. The business model is defined. Here's the roadmap:

- **XCM Integration** — Use Polkadot's Cross-Consensus Messaging to let agents be purchased from any parachain, not just Polkadot Hub. One agent, accessible across the entire Polkadot ecosystem.
- **Execution Verification Oracle** — Bring real agent performance metrics (latency, success rate, uptime) on-chain so users can verify what they're paying for before buying access.
- **DAO Governance** — Transition `FEE_MANAGER_ROLE` to a token-weighted governance module. `$AGT` holders vote on fee structures and protocol parameters.
- **Agent Composability** — Let agents call other agents on-chain. Build complex AI pipelines where each step is a separate registered agent, each creator earns their share.

---

## 👨‍💻 Built For

**OpenGuild × Web3 Foundation — Polkadot Solidity Hackathon 2026**

Tracks: **EVM Smart Contract (AI-Powered dApps)** + **OpenZeppelin Sponsor Track**

---

<div align="center">

**Agentra is infrastructure. It's not a product you use once and forget — it's the layer that makes the next decade of AI agent development economically viable and trustless.**

[🎬 Demo Video](https://canva.link/uxmz5s79nca4jxi) &nbsp;·&nbsp; [📦 GitHub](https://github.com/iammohit64/agentra-polkadot)

</div>
