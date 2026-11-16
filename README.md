🐢 LCOINSWAP

"The turtle doesn't run, it walks."
A community-driven swap tool for the $LCOIN token on Solana. A simplified, fee-free, and transparent alternative that seamlessly interacts with the Pump.fun bonding curve and the open market (via Jupiter).

📖 Table of Contents

1. Philosophy and Goal
2. Key Features
3. System Architecture
4. Repository Structure
5. Tech Stack
6. Prerequisites
7. Local Installation & Setup
8. Contributing Guide
9. Testing Plan
10. Security and Transparency
11. License


🐢 Philosophy and Goal

LCOINSWAP was born from the LCOIN community with a simple premise: make token access easy without relying on complex interfaces or hidden fees.

While $LCOIN is on the Pump.fun bonding curve, LCOINSWAP acts as a friendly abstraction layer. When $LCOIN graduates to the open market, LCOINSWAP automatically redirects swaps through the best Solana pools (via Jupiter), without the user having to switch platforms.

Golden rule: LCOINSWAP charges no fees. It is a 100% community tool. The only costs are the Solana network fees (gas) and, in the graduated phase, the market aggregator fee.


✨ Key Features

· Frictionless Swap: Minimalist interface to exchange SOL ↔ LCOIN (and future pairs).
· Automatic Redirection: On-chain detection of the bonding curve state (complete: true/false) to use PumpPortal (curve) or Jupiter (open market) without interrupting the user.
· Transparency Panel: Live table with the latest transactions (type, net amount, wallet, Solscan link). Anyone can verify there is no manipulation.
· Zero Fees: LCOINSWAP Fee: 0% ✅ visible on every quote.
· Pre-swap Validation: SOL/LCOIN balance check before building any transaction.
· Minimalist Dark Mode: Modern design, Inter typography, mint green and amber accents.

🏗 System Architecture

LCOINSWAP consists of three layers:

[Frontend (React/Next.js)]
       | (REST API / WebSocket)
       
[Backend (Node.js/Express)]
       │  ├── Solana RPC (Helius/QuickNode)
       │  ├── PumpPortal API (curve)
       │  └── Jupiter API (graduated)
       
[Solana Blockchain]


Security principle: The backend never receives private keys. It only builds unsigned transactions and returns them to the frontend so the user can sign them in their wallet (Phantom/Solflare).

Swap Flow (Summary)

1. User connects wallet → backend reads balance.
2. Backend calls /api/status → Is LCOIN on the curve or graduated?
3. If curve: backend calls PumpPortal → returns serialized tx.
4. If graduated: backend calls Jupiter /order → returns signable tx.
5. Frontend displays fee breakdown (0% fee).
6. User signs in wallet.
7. Frontend sends tx (curve: direct RPC; graduated: Jupiter /execute).
8. Transparency panel updates with the Solscan hash.


📁 Repository Structure

```
lcionswap/
├── frontend/                 # React/Next.js application
│   ├── src/
│   │   ├── components/       # SwapCard, TransparencyPanel, WalletButton, etc.
│   │   ├── hooks/            # useWallet, useQuote, useTransparency
│   │   ├── styles/           # Design system (colors, typography)
│   │   └── config/           # UI constants (messages, endpoints)
│   └── package.json
│
├── backend/                  # Node.js/Express API
│   ├── src/
│   │   ├── config/           # env.js, constants.js
│   │   ├── services/         # solanaRpc.js, transparency.js, pumpPortal.js, jupiter.js, state.js
│   │   ├── routes/           # transparency.js, quote.js, swap.js, status.js
│   │   ├── utils/            # format.js, errors.js
│   │   └── index.js
│   ├── tests/                # Jest (unit + integration)
│   ├── .env.example
│   └── package.json
│
├── docs/                     # Additional documentation
│   ├── ARCHITECTURE.md       # Backend logic details
│   ├── TESTING.md            # Detailed testing plan
│   └── DESIGN_SYSTEM.md      # Colors, typography, components
│
├── .gitignore
├── README.md                 # This file
└── LICENSE
```

---

🛠 Tech Stack

Layer     |.  Technology                 |Rationale
Frontend  | React / Next.js + TypeScript |Reusable components, optional SSR, type safety.
Styling   |Tailwind CSS + CSS Modules.   |Minimalism, consistency with the design system.
Wallet    |@solana/wallet-adapter        |Native support for Phantom, Solflare, Backpack.
Backend   |Node.js + Express + TypeScript|Lightweight, stateless, easy to deploy (Railway, Render, Vercel).
Blockchain|@solana/web3.js               |Connection to the Solana RPC.
External  | PumpPortal (curve)+Jupiter.  |Transaction building without custodying funds.
 APIs        Meta-Aggregator (graduated)  
Testing   |Jest + k6 (stress)            |Unit and integration coverage.
Deployment|Vercel (frontend) + Railway/Render (backend) |Low cost, scalable, community-friendly.


📋 Prerequisites

· Node.js v18 or higher.
· npm or pnpm.
· Git.
· A Solana wallet (Phantom/Solflare) for devnet testing.
· A Jupiter API Key (free at Jupiter Portal).
· A Solana RPC (recommended: Helius or QuickNode, free tier).


🚀 Local Installation & Setup

1. Clone the repository

bash
git clone https://github.com/your-username/LCOINSwap.git
cd lcionswap


2. Configure the Backend

bash
cd backend
npm install
cp .env.example .env


Edit .env with your credentials:

env
# Solana RPC (devnet for testing)
RPC_URL=https://devnet.helius-rpc.com/?api-key=YOUR_API_KEY

# Jupiter API Key
JUP_API_KEY=your_jupiter_api_key

# Token addresses (test devnet)
LCOIN_MINT=LCOIN_Devnet_Mint_Address
SOL_MINT=So11111111111111111111111111111111111111112

# Server port
PORT=3001


Start the backend:

bash
npm run dev


3. Configure the Frontend

bash
cd ../frontend
npm install
cp .env.example .env.local


Edit .env.local:

env
NEXT_PUBLIC_BACKEND_URL=http://localhost:3001
NEXT_PUBLIC_SOLANA_NETWORK=devnet


Start the frontend:

bash
npm run dev


Open http://localhost:3000 in your browser.

4. Get Devnet SOL

bash
solana airdrop 2 YOUR_WALLET_ADDRESS --url devnet


Or use the Helius faucet.



🤝 Contributing Guide

The community is the heart of LCOINSWAP! If you want to contribute:

1. Fork the repository.
2. Create a branch for your feature: git checkout -b feature/new-feature.
3. Follow the design system (see docs/DESIGN_SYSTEM.md).
4. Make sure tests pass: npm test.
5. Commit with clear messages (follow Conventional Commits).
6. Open a Pull Request describing the change and its motivation.

Golden rules for PRs:

· Do not add fees to the tool.
· Do not expose private keys or API keys in the frontend.
· Keep error messages in the community tone (see docs/ERRORS.md).
· Include tests for new features.



🧪 Testing Plan

Tests are divided into 5 phases (see docs/TESTING.md for full details):

Phase What is tested Environment
1 Unit tests (formatting, parsing, error mapping) Local (Jest)
2 Integration (full swap flow, panel) Devnet
3 Stress (100+ concurrent users) Devnet + k6
4 Security (injection, replay, key exposure) Local + Devnet
5 UAT (real community users) Devnet

Commands:

bash
cd backend
npm test                  # Unit tests
npm run test:integration  # Devnet integration tests


🔒 Security and Transparency

· No private keys in the backend. The backend only builds transactions; the user signs in their wallet.
· Protected API keys. JUP_API_KEY and RPC_URL live in .env and are never exposed to the frontend.
· Hardcoded mint. The backend only accepts the official LCOIN mint address (defined in constants.js). Any other is rejected.
· Maximum slippage. The backend enforces a 5% (500 bps) limit to prevent accidental signatures with absurd slippage.
· Transparency panel. Every swap is verifiable on Solscan. No hidden prices or manipulation.

📄 License

This project is licensed under the MIT License. See the LICENSE file for details.


🐢 One Last Reminder

"The turtle doesn't run, it walks."

LCOINSWAP doesn't aim to be the fastest or flashiest tool. It aims to be reliable, transparent, and community-driven. Step by step, but with a firm step.


Questions? Open an Issue or join the LCOIN community.


Notes for the development team (do not include in the public README)

· The exact parsing logic for Pump.fun and Jupiter should go in docs/ARCHITECTURE.md, not the README.
· The final error messages (the ones we defined) should be in frontend/src/config/messages.ts and backend/src/utils/errors.js.
· The design system (colors, typography) should be in docs/DESIGN_SYSTEM.md and reflected in tailwind.config.js.
· The full testing plan (the 26 integration scenarios) should be in docs/TESTING.md.
