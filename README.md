<div align="center">

# FundChain

### Decentralized Crowdfunding DApp

Trustless Ethereum crowdfunding with on-chain campaign logic, MetaMask transactions, a Vue 3 dashboard, donor refunds, and owner withdrawals.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Vue](https://img.shields.io/badge/Vue-3-42b883?logo=vue.js&logoColor=white)](https://vuejs.org/)
[![Vite](https://img.shields.io/badge/Vite-8-646CFF?logo=vite&logoColor=white)](https://vitejs.dev/)
[![Solidity](https://img.shields.io/badge/Solidity-0.8.17-363636?logo=solidity&logoColor=white)](contracts/Crowdfunding.sol)
[![Web3.js](https://img.shields.io/badge/Web3.js-4-F16822?logo=web3.js&logoColor=white)](https://web3js.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3-06B6D4?logo=tailwindcss&logoColor=white)](https://tailwindcss.com/)
[![MetaMask](https://img.shields.io/badge/MetaMask-wallet-E2761B?logo=metamask&logoColor=white)](https://metamask.io/)

> Projet réalisé en binôme avec Abdelkrim BELLAGNECH

</div>

---

## Overview

FundChain is a small proof-of-concept DApp that demonstrates a complete crowdfunding lifecycle on Ethereum. A Solidity smart contract owns the campaign state and funds, while a Vue 3 frontend connects to MetaMask through Web3.js to read campaign data and submit transactions.

The app is intentionally backend-free. Donations, withdrawal eligibility, refund eligibility, donor balances, campaign deadlines, and funding progress are enforced by the smart contract rather than a traditional server.

It is an educational project for learning DApp architecture, wallet integration, and local blockchain development. It is not production-audited financial software.

## Highlights

- Create and manage crowdfunding campaigns through deployed contract addresses.
- Donate ETH from MetaMask while a campaign is active.
- Track campaign goal, total raised, donors, countdown, and funding status.
- Detect owner and donor roles from the connected wallet.
- Allow owners to withdraw only after the campaign has ended and the goal was reached.
- Allow donors to claim refunds only after the deadline when the campaign failed.
- Persist multiple campaign addresses locally with `localStorage`.
- Show transaction lifecycle feedback with pending, success, and error toasts.
- React to MetaMask account and network changes.

## Tech Stack

| Layer | Technology |
| --- | --- |
| Smart contract | Solidity `^0.8.17` |
| Local blockchain | Ganache |
| Deployment workflow | Remix IDE |
| Frontend | Vue 3, Vite |
| Styling | Tailwind CSS |
| Ethereum client | Web3.js |
| Wallet | MetaMask |
| Persistence | Browser `localStorage` |

## Architecture

```text
Browser
+-- Vue 3 frontend
|   +-- Campaign views
|   +-- Owner dashboard
|   +-- Donate / withdraw / refund panels
|   +-- Toast transaction feedback
|
+-- MetaMask
|   +-- Account connection
|   +-- Network selection
|   +-- Transaction signing
|
+-- Web3.js
    +-- Contract reads
    +-- Contract writes

Ganache / Ethereum network
+-- Crowdfunding.sol
    +-- donate()
    +-- withdraw()
    +-- refund()
    +-- getCampaignInfo()
    +-- getDonorsCount()
```

## Smart Contract

`contracts/Crowdfunding.sol` implements a single campaign with a fixed owner, goal, deadline, donor ledger, and event log.

### Lifecycle

```text
Deploy campaign
    |
    v
Active: accepts donations until deadline
    |
    v
Ended
    +-- Goal reached: owner can withdraw raised ETH
    +-- Goal missed: donors can claim refunds
```

### Contract API

| Function | Access | Purpose |
| --- | --- | --- |
| `donate()` | Anyone | Sends ETH to the active campaign. |
| `withdraw()` | Owner | Transfers the contract balance to the owner after success conditions are met. |
| `refund()` | Donors | Returns a donor's contribution if the campaign ended below goal. |
| `getCampaignInfo()` | Public view | Returns title, description, goal, raised amount, time left, and goal status. |
| `getDonorsCount()` | Public view | Returns the number of unique donor addresses. |

### Events

| Event | Emitted when |
| --- | --- |
| `DonationReceived(address donor, uint256 amount)` | A donation succeeds. |
| `GoalReached(uint256 totalRaised)` | The campaign reaches or passes its goal. |
| `FundsWithdrawn(address owner, uint256 amount)` | The owner withdraws successfully. |
| `RefundIssued(address donor, uint256 amount)` | A donor refund succeeds. |

## Screenshots

| Donor campaign view | Campaign list |
| --- | --- |
| ![Campaign view donor mode](Assets/Screenshot%20from%202026-04-15%2002-29-02.png) | ![Campaign list donor view](Assets/Screenshot%20from%202026-04-15%2002-29-12.png) |

| Owner dashboard | Add campaign |
| --- | --- |
| ![Owner dashboard](Assets/Screenshot%20from%202026-04-15%2002-29-38.png) | ![Add campaign modal](Assets/Screenshot%20from%202026-04-15%2002-29-52.png) |

| MetaMask transaction | Success toast |
| --- | --- |
| ![MetaMask donation confirmation](Assets/Screenshot%20from%202026-04-15%2002-30-30.png) | ![Donation success toast](Assets/Screenshot%20from%202026-04-15%2002-30-38.png) |

## Getting Started

### Prerequisites

- Node.js `18+`
- Ganache
- MetaMask
- Remix IDE

### Install

```bash
git clone https://github.com/Abdelkrim7Be/crowdfunding-dapp.git
cd crowdfunding-dapp
npm install
```

### Start Ganache

Create a Ganache Ethereum workspace and keep it running.

| Field | Value |
| --- | --- |
| RPC URL | `http://127.0.0.1:7545` |
| Chain ID | `1337` |
| Currency symbol | `ETH` |

Import at least two Ganache accounts into MetaMask if you want to test both owner and donor flows.

### Deploy The Contract

1. Open [Remix IDE](https://remix.ethereum.org).
2. Create `contracts/Crowdfunding.sol` and paste the contract from this repository.
3. Compile with Solidity `0.8.17` or newer compatible `0.8.x`.
4. In **Deploy & Run Transactions**, select **Injected Provider - MetaMask**.
5. Connect MetaMask to Ganache.
6. Deploy with constructor arguments:

```text
_title: "My First Campaign"
_description: "Raising ETH for my project"
_goalAmount: 5
_durationDays: 7
```

7. Copy the deployed contract address.

### Configure The Frontend

Update the address in `src/web3.js`:

```js
export const CONTRACT_ADDRESS = "0xYourDeployedContractAddress";
```

If the contract ABI changes, recompile in Remix and update `CONTRACT_ABI` in `src/web3.js`.

### Run The App

```bash
npm run dev
```

Open `http://localhost:5173`, connect MetaMask, and interact with the deployed campaign.

## Project Structure

```text
crowdfunding-dapp/
+-- contracts/
|   +-- Crowdfunding.sol
+-- src/
|   +-- App.vue
|   +-- main.js
|   +-- style.css
|   +-- web3.js
|   +-- composables/
|   |   +-- useCampaigns.js
|   +-- components/
|       +-- AddCampaignModal.vue
|       +-- CampaignCard.vue
|       +-- CampaignCardPreview.vue
|       +-- CampaignList.vue
|       +-- DemoGuide.vue
|       +-- DonateSection.vue
|       +-- NavBar.vue
|       +-- OwnerDashboard.vue
|       +-- OwnerSection.vue
|       +-- RefundSection.vue
|       +-- ToastNotification.vue
+-- Assets/
+-- public/
+-- index.html
+-- package.json
+-- tailwind.config.js
+-- vite.config.js
```

## Available Scripts

| Command | Description |
| --- | --- |
| `npm run dev` | Start the Vite development server. |
| `npm run build` | Build the static production bundle into `dist/`. |
| `npm run preview` | Preview the production build locally. |

## Static Deployment

Build the frontend:

```bash
npm run build
```

Deploy the generated `dist/` directory to any static hosting provider such as Vercel, Netlify, Cloudflare Pages, or GitHub Pages.

For a public deployment, deploy the contract to a public testnet such as Sepolia and update `CONTRACT_ADDRESS` before building.

## Security Notes

This repository is a learning project and has not been audited. Do not use it with real funds.

Important production improvements would include:

- Hardhat or Foundry tests for every contract state transition.
- A contract factory instead of storing campaign addresses in browser storage.
- OpenZeppelin `Ownable` and `ReentrancyGuard`.
- Event indexing through The Graph or another indexer.
- Public testnet deployment and verified source code.
- WalletConnect support for mobile wallets.
- IPFS or another decentralized storage layer for campaign media.

See [SECURITY.md](SECURITY.md) for vulnerability reporting guidance.

## Roadmap

- Add a Hardhat or Foundry test suite.
- Replace Remix deployment with scripted deployments.
- Add a campaign factory contract.
- Add Sepolia configuration.
- Add event-based history views for donations, withdrawals, and refunds.
- Add campaign image support through IPFS.

## License

This project is licensed under the [MIT License](LICENSE).
