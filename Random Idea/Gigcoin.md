 This project is a local demo of a decentralized gig marketplace built around a
  smart-contract escrow. The main pieces are:

  - A React frontend with three tabs: asker, worker, and a demo market view in
    frontend/src/App.tsx:27
  - A Solidity escrow contract that holds funds, worker stake, validation votes,
    and final payouts in contracts/src/JobEscrow.sol:10
  - A GigCoin ERC20-like token in contracts/src/Token.sol:4
  - A small FastAPI backend for server-side job creation and result decryption
    in backend/app/api/jobs.py:7
  - Some worker/crypto placeholders that look intended for off-chain execution,
    but are not fully wired yet in worker/tasks/compute.py:1 and worker/
    executor/runner.py:1

  How the main flow is supposed to work:

  1. The requester creates and funds a job.
     In the frontend, CreateJob collects title, description, USD price,
     requester key, and deadline, then calls createAndFundJob(...) in frontend/
     src/components/CreateJob.tsx:31 and frontend/src/web3/contract.ts:168.
     That does 3 wallet transactions:
      - createJob
      - token approve
      - fundJob
  2. The job is stored on-chain.
     The contract stores requester, worker, USD price, token amount, result CID/
     hash, stake, state, timestamps, and deadline in contracts/src/
     JobEscrow.sol:24.
  3. The worker accepts the job by staking tokens.
     acceptJob moves the job from FUNDED to IN_PROGRESS in contracts/src/
     JobEscrow.sol:77.
     The worker modal in frontend/src/components/WorkerActions.tsx:80 first
     approves token spending, then calls acceptJob.
  4. The worker is supposed to submit work.
     The contract supports submitWork(jobId, resultHash, resultCID) in
     contracts/src/JobEscrow.sol:85, and the frontend has a helper for it in
     frontend/src/web3/contract.ts:235.
     But the current worker UI does not actually call submitCompletedWork.
     Instead, after “Submit gig”, it just calls mockReward(...) and shows a fake
     success state in frontend/src/components/WorkerActions.tsx:87. So that part
     is demo-only right now.
  5. Validators can vote on submitted work.
     The contract has validator staking, voting, and validation finalization in
     contracts/src/JobEscrow.sol:55 and contracts/src/JobEscrow.sol:92.
     The frontend has wrappers for stakeAsValidator, voteOnJob, and
     finalizeValidation in frontend/src/web3/contract.ts:215, but I did not find
     UI that actually uses them.
  6. If validation passes, the contract settles payment.
     finalize(jobId) burns a small amount, pays treasury, pays worker, returns
     worker stake, and mints extra GigCoin rewards to worker and validators in
     contracts/src/JobEscrow.sol:117.

  How the frontend gets data:

  - It connects directly to MetaMask via ethers in frontend/src/hooks/
    useWallet.ts:15
  - It reads jobCounter() and then jobs(id) directly from the contract in
    frontend/src/components/JobList.tsx:31
  - It stores title/description/deadline only in browser localStorage, not on-
    chain, via frontend/src/web3/contract.ts:284

  That means the human-readable job metadata is local to one browser. On-chain,
  only the escrow fields exist.

  What the backend does:

  - POST /jobs/create creates a job, approves token spending, and funds it from
    a backend private key in backend/app/api/jobs.py:10 and backend/app/
    services/blockchain.py:92
  - GET /jobs/{id}/result reads resultCID from chain, downloads JSON from
    Pinata/IPFS, and tries to decrypt it in backend/app/api/jobs.py:18

  How local startup works:

  - run.sh:1 starts a Hardhat node, deploys contracts, starts FastAPI, and
    starts Vite
  - Deployment writes the escrow address into both backend/.env and
    frontend/.env, and copies the ABI into the frontend in contracts/script/
    deploy.ts:39

  Important reality check: parts of this are incomplete

  - The worker pipeline is placeholder code, not a real compute/execution
    system.
  - The frontend worker submit flow is fake right now; it claims a mock reward
    instead of submitting work to the contract.
  - The encryption/decryption path is incomplete. backend/app/services/
    decryption.py expects crypto.decrypt.decrypt_data, but crypto/decrypt.py is
    empty in this repo.
  - The backend is mostly separate from the frontend. The frontend does not call
    backend APIs for normal job flow.
  - Token amount handling is inconsistent between frontend and backend. The
    frontend uses 18-decimal units with parseUnits, while backend pricing
    returns plain integers in backend/app/services/pricing.py:1.
  - The JobState enum includes SUBMITTED, but submitWork moves directly to
    VALIDATING, so SUBMITTED is currently unused in contracts/src/
    JobEscrow.sol:22.

  If you want, I can do a second pass and explain it as one concrete example,
  like “Alice posts a job, Bob accepts it, validators vote, Bob gets paid,”
  using the actual functions and files step by step.