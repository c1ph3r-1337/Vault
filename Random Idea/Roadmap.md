# 0) What you’re building (crisp definition)

A **decentralized gig/compute marketplace** with:

- USD-denominated pricing (via oracle)
- token-only settlement (ERC-20)
- escrow + staking + slashing (smart contracts)
- encrypted result delivery (asymmetric + hybrid)
- off-chain execution + verification
- IPFS-backed storage for encrypted payloads

---

# 1) Roadmap (start → finish)

## Phase 1 — System design (no code)

Deliverables:

- State machine (job lifecycle)
- Data schemas (Job, Submission, Dispute)
- Tokenomics knobs (fees %, stake %, burn %)
- Threat model (freeloading, non-reveal, oracle issues)

Output:

- `docs/architecture.md`
- `docs/state-machine.md`
- `docs/tokenomics.md`

---

## Phase 2 — Smart contracts (foundation)

Goal: **trust + money flows**

Build on an EVM chain using Solidity.

Contracts:

- `Token.sol` (ERC-20)
- `OracleAdapter.sol` (price feed via Chainlink)
- `JobEscrow.sol` (core logic)

Key features:

- create job (USD → token conversion)
- fund escrow
- accept job (stake lock)
- submit (CID + commitments)
- verify (hook)
- finalize (pay, burn fee, unlock stake)
- dispute + slashing

Output:

- `contracts/`
- tests (Hardhat)

---

## Phase 3 — Backend (orchestration layer)

Goal: glue everything together

Tech: Python (FastAPI)

Responsibilities:

- job API
- oracle read (fallback / caching)
- IPFS upload
- worker coordination
- verification logic (auto checks)

Output:

- `backend/`

---

## Phase 4 — Worker node (execution engine)

Goal: actually do the work

Responsibilities:

- pull jobs
- stake + accept
- compute
- encrypt result
- upload to IPFS
- submit CID + hash

Output:

- `worker/`

---

## Phase 5 — Encryption protocol

Goal: enforce “pay → then read”

Implement:

- requester keypair (pk_R, sk_R)
- hybrid encryption:
    - AES for data
    - RSA/EC for key

Flow:

- worker encrypts result
- only requester can decrypt

Output:

- `crypto/` (shared lib used by backend + worker)

---

## Phase 6 — Frontend (control panel)

Goal: usable interface

Tech: React + TypeScript

Features:

- post job
- see token price
- fund escrow
- track job state
- decrypt results

Output:

- `frontend/`

---

## Phase 7 — Integration

Goal: full pipeline works end-to-end

Test:

1. user posts job
2. tokens escrowed
3. worker accepts + stakes
4. worker submits encrypted result
5. verify
6. payment released
7. requester decrypts

---

## Phase 8 — Hardening (important)

- timeout handling
- non-reveal penalties
- gas optimization
- edge cases

---

# 2) Final system flow (what you will end with)

User → buys token → creates job → escrow  
  
Worker → stakes → completes job → encrypts → uploads (IPFS)  
  
Contract → verifies → releases payment → burns fee  
  
Requester → decrypts result  
  
Market (DEX) → adjusts token price

---

# 3) File / Repo structure (clean + scalable)

Use a **monorepo**:

project-root/  
│  
├── contracts/                 # Solidity  
│   ├── src/  
│   │   ├── Token.sol  
│   │   ├── JobEscrow.sol  
│   │   ├── OracleAdapter.sol  
│   │  
│   ├── test/  
│   ├── script/  
│   ├── hardhat.config.ts  
│  
├── backend/                   # FastAPI  
│   ├── app/  
│   │   ├── main.py  
│   │   ├── api/  
│   │   ├── services/  
│   │   │   ├── blockchain.py  
│   │   │   ├── pricing.py  
│   │   │   ├── ipfs.py  
│   │   │   ├── verification.py  
│   │   ├── models/  
│   │   ├── schemas/  
│   │  
│   ├── requirements.txt  
│  
├── worker/                    # compute node  
│   ├── worker.py  
│   ├── tasks/  
│   ├── executor/  
│   ├── submitter.py  
│  
├── crypto/                    # shared encryption lib  
│   ├── encrypt.py  
│   ├── decrypt.py  
│   ├── keygen.py  
│  
├── frontend/                  # React app  
│   ├── src/  
│   │   ├── components/  
│   │   ├── pages/  
│   │   ├── hooks/  
│   │   ├── web3/  
│   │  
│   ├── package.json  
│  
├── scripts/                   # dev scripts  
│   ├── deploy.sh  
│   ├── start-dev.sh  
│  
├── docs/                      # critical for you  
│   ├── architecture.md  
│   ├── tokenomics.md  
│   ├── protocol.md  
│   ├── api-spec.md  
│  
├── docker/                    # optional  
│   ├── docker-compose.yml  
│  
└── README.md

---

# 4) Development order (do NOT change this)

Follow strictly:

### Step 1

Define:

- job lifecycle
- contract interfaces

### Step 2

Write smart contracts (minimal version)

### Step 3

Write backend that talks to contract

### Step 4

Write worker (fake computation first)

### Step 5

Add encryption

### Step 6

Add frontend

### Step 7

Refine economics

---

# 5) Minimal first milestone (what we build next)

Before scaling, you build **MVP v0**:

- ERC20 token
- simple escrow contract
- backend API
- worker that returns dummy result
- IPFS upload
- encrypted result

If that works → everything else is iteration.

---

# 6) Critical mindset

Don’t try to build:

- full DAO
- complex AI pricing
- perfect tokenomics

on day 1.

Build:

> **“one job → one worker → one payment → one decrypt”**