# BlockCred — Blockchain-Based Academic Credential Verification

A college-project MVP using React + Vite, FastAPI + SQLite, Solidity + Hardhat, and a local Ethereum-compatible chain.

## Prototype status
This repository is a starter implementation, not a production-ready credential platform. It includes a contract-backed issuance/verification path when the local blockchain is configured. Do not describe any feature as tested on your machine until you run the steps below.

## Architecture
- React frontend: `http://localhost:5173`
- FastAPI backend: `http://127.0.0.1:8000` (`/docs` for Swagger)
- SQLite database: `backend/blockcred.db`
- Hardhat local node: `http://127.0.0.1:8545`
- Solidity registry stores only a credential hash and revocation status.

## Prerequisites
Install:
- Python 3.11+
- Node.js 20+
- Git (optional)
- VS Code (optional)

Check versions in PowerShell:
```powershell
py --version
node --version
npm --version
```

## Setup (Windows PowerShell)

### Terminal 1 — blockchain
```powershell
cd blockcred\blockchain
npm install
npx hardhat compile
npx hardhat node
```
Leave this terminal running. Hardhat prints local test accounts and private keys. These are local-only test keys; never use them on a public network.

### Terminal 2 — deploy contract
```powershell
cd blockcred\blockchain
npx hardhat run scripts/deploy.js --network localhost
```
Copy the displayed contract address into `backend\.env` as `CONTRACT_ADDRESS=0x...`.
Copy `blockchain\artifacts\contracts\AcademicCredentialRegistry.sol\AcademicCredentialRegistry.json` to `backend\contract_abi.json` (the deployment script also attempts to copy it).

### Terminal 3 — backend
```powershell
cd blockcred\backend
py -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
# Edit .env: set CONTRACT_ADDRESS to the deployed address.
# For local Hardhat only, set ISSUER_PRIVATE_KEY to one of the local accounts printed by `npx hardhat node`.
uvicorn app.main:app --reload
```
If PowerShell blocks activation, run:
```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```
Swagger: http://127.0.0.1:8000/docs

### Terminal 4 — frontend
```powershell
cd blockcred\frontend
npm install
npm run dev
```
Open http://localhost:5173.

## Demo flow
1. Start the local blockchain and deploy the contract.
2. Configure the backend `.env` with contract address and a local Hardhat private key.
3. Start backend and frontend.
4. Issue a fictional credential from the Issue page.
5. Open its verification page or enter its credential ID on Verify.
6. Try a random ID to demonstrate an invalid result.
7. Revoke a credential from the credential details page, then verify it again.

## Important limitations
- Local development issuer keys and demo authorization are not production security.
- This prototype does not independently establish that an institution is accredited or that the original award decision was truthful.
- A credential hash may still leak information if the source data has low entropy. Keep personal data off-chain and consider salted commitments or privacy-preserving schemes for real deployments.
- Public verification endpoints disclose selected credential metadata. Use fictional demo data.
- The application is intended for a local presentation, not real student records.

## Tests
Backend:
```powershell
cd blockcred\backend
.\.venv\Scripts\Activate.ps1
pytest
```
Smart contract:
```powershell
cd blockcred\blockchain
npx hardhat test
```
Frontend build:
```powershell
cd blockcred\frontend
npm run build
```
