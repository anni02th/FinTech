# FinTrustChain

FinTrustChain is a fintech platform combining traditional loan management with a blockchain-backed contract/trust index layer. This repository contains the backend API and services (Express/Node) and a `client/` Next.js UI that provides borrower, lender, guarantor, and admin experiences.

This README documents repository structure, how to run the project, environment variables required, available npm scripts, a clear API surface (endpoints and required fields), and a recommended UI structure with fields/components per page.

## Table of contents
- Project overview
- Quick start (local & Docker)
- Environment variables
- Scripts
- Architecture & important files
- API reference (endpoints + request/response shapes)
- UI: pages, components & field requirements
- Data models (summary)
- Authentication, middleware and error flow
- Tips: testing, Postman, and deployment notes
- Contributing

---

## Project overview

Purpose: provide a platform for borrowers to request loans, lenders to underwrite and fund loans, guarantors to provide backing, and allow contracts to be created and stored/verified on a blockchain layer. It includes payment settlement, notifications, PDF generation for loan brochures and contracts, and a trust-index service for reputation scoring.

Key folders:
- `server.js` — App entry
- `config/` — DB and other configuration
- `controllers/` — Express route controllers
- `routes/` — Route definitions
- `models/` — Mongoose models (MongoDB)
- `services/` — Business logic (payments, pdf, trust index)
- `middlewares/` — authentication and rate limiting
- `client/` — Next.js frontend

## Quick start

Prerequisites:
- Node.js (v16+ recommended)
- npm or yarn
- MongoDB (local or hosted)
- (Optional) Docker & docker-compose

1) Install dependencies

```powershell
cd FinTrustChain
npm install
cd ../client
npm install
```

2) Copy or create your `.env` file from `.env.example` in the backend root:

Example env variables (see below for the full list).

3) Run backend

```powershell
# from FinTrustChain/
node server.js
# or if you have nodemon configured (dev)
npm run dev
```

4) Run frontend

```powershell
cd client
npm run dev
```

5) Docker (optional)

```powershell
# from repository root (where docker-compose.yml is)
docker-compose up --build
```

## Environment variables

Create a `.env` file with at least the following keys (adapt names to match your code):

- PORT=3001
- MONGO_URI=mongodb://user:pass@host:27017/fintrust
- JWT_SECRET=verysecret
- JWT_EXPIRES_IN=7d
- EMAIL_HOST=smtp.example.com
- EMAIL_PORT=587
- EMAIL_USER=you@example.com
- EMAIL_PASS=strongpassword
- RATE_LIMIT_WINDOW_MS=60000
- RATE_LIMIT_MAX=100
- PAYMENT_PROVIDER_KEY=...
- WEB3_PROVIDER_URL=https://mainnet.infura.io/v3/...
- CONTRACT_ADDRESS=0x...
- BLOCKCHAIN_PRIVATE_KEY=0x...

Notes:
- `MONGO_URI`, `JWT_SECRET`, and any payment or blockchain keys are required for production.
- If the code expects specific variable names, ensure alignment with `config/db.js` and service files.

## NPM scripts (recommended)

The repository's `package.json` typically contains scripts. Common scripts to add/use:

- `start` — start the server in production (node server.js)
- `dev` — start server with nodemon for development
- `lint` — run ESLint
- `test` — run unit tests (if present)

Example (run locally):

```powershell
npm run dev
```

## Architecture & important files

- `server.js`: Express bootstrap, middleware registration, route mounting.
- `config/db.js`: MongoDB connection.
- `middlewares/authMiddleware.js`: JWT authentication guard, extracts user data and role.
- `middlewares/rateLimiter.js`: rate limiting to protect endpoints.
- `controllers/*Controller.js`: business endpoints (auth, users, loans, payments, contracts).
- `routes/*Route.js`: route -> controller binding.
- `models/*.js`: Mongoose schemas for Users, LoanRequest, LoanBrochure, Contract, Endorsement, GuarantorRequest, Notification, Transaction.
- `services/`: contains domain logic (paymentService, pdfService, trustIndexService, notificationService, loanSettlementServices)

## API reference (high-level)

Notes: exact route paths are in `routes/` but the controllers indicate resource names and expected behavior. Below are the recommended endpoints and required fields per resource.

Common HTTP response shapes:
- Success: { success: true, data: <object> }
- Error: { success: false, error: { message: string, details?: any } }

Authentication: Bearer JWT token in `Authorization: Bearer <token>` header for protected routes.

1) Auth
- POST /api/auth/register
  - Body: { name, email, password, role } // role = borrower|lender|guarantor|admin
  - Response: { token, user }

- POST /api/auth/login
  - Body: { email, password }
  - Response: { token, user }

- POST /api/auth/logout
  - Protected: invalidates token (if token store) or client-side removal.

2) Users
- GET /api/users/:id — get user profile (protected or public trimmed)
- PUT /api/users/:id — update profile (protected)
  - Body: { name?, email?, phone?, address?, profilePicture? }
- GET /api/users — admin only: list users

Fields for User model (minimum):
- name (string), email (string, unique), password (hashed), role (enum), phone, address, reputationScore, createdAt

3) Loan Requests
- POST /api/loan-requests
  - Body: {
      borrowerId, // usually from token; optional if admin
      amount, // number or string
      termMonths, // int
      purpose, // string
      interestRate, // number
      collateralDescription?,
      repaymentSchedule? // optional structure
    }
  - Response: created loan request

- GET /api/loan-requests — list (query params: status, borrowerId, page, size)
- GET /api/loan-requests/:id — details with endorsements, guarantorRequests, contract status
- PUT /api/loan-requests/:id/status — update status (submitted, under-review, funded, active, closed, rejected)
  - Body: { status, comment? }

4) Loan Brochures
- POST /api/loan-brochures
  - Body: { title, description, minAmount, maxAmount, termMonths, interestRate, termsText }
- GET /api/loan-brochures

5) Guarantor Requests
- POST /api/guarantor-requests
  - Body: { loanRequestId, guarantorId, guaranteeAmount, message }
- PUT /api/guarantor-requests/:id/respond
  - Body: { accept: boolean }

6) Endorsements
- POST /api/endorsements
  - Body: { loanRequestId, endorserId, comment, amount } // amount optional; endorsement may raise visibility
- GET /api/endorsements?loanRequestId=...

7) Contracts
- POST /api/contracts
  - Body: { loanRequestId, parties: [{role, userId}], terms, startDate, endDate }
  - Response: contract record plus (optional) blockchain tx info

- GET /api/contracts/:id

8) Payments / Transactions
- POST /api/payments
  - Body: { payerId, payeeId, loanRequestId, amount, method, metadata? }
  - Response: { transactionId, status, providerResponse }

- GET /api/transactions?userId=...

9) Notifications
- GET /api/notifications?userId=...
- POST /api/notifications
  - Body: { userId, title, message, type }

10) Dashboard
- GET /api/dashboard/summary — returns metrics (totalLoans, totalOutstanding, activeLoans, recentTransactions)

Authentication & Roles
- Protect routes via `authMiddleware.js`. Roles (borrower, lender, guarantor, admin) gate actions such as funding a loan, viewing all users, or changing statuses.

Errors and validation
- All POST/PUT endpoints should validate required fields. Return 400 for missing/wrong fields, 401 for unauthorized, 403 for forbidden, 404 for not found, 500 for server errors.

## UI: pages, major components & required fields

The `client/` Next.js app should provide the following pages and components. For each page I list the main fields and components the UI needs.

General components
- Header (auth state, nav links)
- Footer
- Notifications panel / toast
- Modal component (confirmations, signing)
- Forms (reusable text inputs, number inputs, date picker, file upload)

Public pages
- Home / Landing
  - Prominent CTAs: Login, Register, Browse loan brochures

- Auth: Login / Register
  - Login fields: email, password
  - Register fields: name, email, password, confirmPassword, phone, role selector

Borrower experience
- Borrower Dashboard
  - Summary cards (active loans, outstanding balance, pending requests)
  - Recent notifications

- Create Loan Request (form)
  - amount, termMonths, interestRate (or accept default), purpose, collateralDescription, supportingDocuments (file upload)

- Loan Request detail
  - Shows endorsements, guarantor offers, contract if generated, repayment schedule, actions (cancel, accept offer)

Lender experience
- Lender Dashboard
  - New applications feed, filters (amount, risk score)

- Lend/Underwrite flow
  - Quick action to fund portion or full: amount to fund, terms, note

Guarantor experience
- Guarantor Dashboard
  - Pending guarantor requests: accept/decline; view risk/borrower info

Contracts & Signing
- Contract viewer with scrollable terms and a 'Sign' button
  - Signing flow may integrate with Web3 wallet (if on-chain signature) or capture an e-signature.

Payments
- Payment UI (pay installments)
  - Select loan, amount, payment method (card, bank, crypto), confirm

Admin pages
- Users management, Loan approvals, Dashboard metrics.

UI data fields mapping (summary):
- User: name, email, phone, role, reputationScore
- LoanRequest: amount, termMonths, interestRate, purpose, status, borrowerId
- LoanBrochure: title, minAmount, maxAmount, term, interestRate, description
- Contract: contractId, loanRequestId, parties[], terms, status, blockchainTxHash

UI notes
- Make sure forms validate on client and server.
- Show clear status badges (Submitted, Under Review, Funded, Active, Closed, Rejected).

## Data models (summary)

User
- id, name, email, passwordHash, role, phone, reputationScore, createdAt

LoanRequest
- id, borrowerId, amount, termMonths, interestRate, status, endorsements[], guarantorRequests[], contractId, createdAt

LoanBrochure
- id, title, minAmount, maxAmount, termMonths, interestRate, description

Contract
- id, loanRequestId, parties, terms, status, blockchainTxHash, createdAt

Transaction
- id, payerId, payeeId, loanRequestId, amount, status, providerResponse, createdAt

Notification
- id, userId, title, message, read, createdAt

## Authentication, middleware & security

- Authentication is JWT-based. Tokens returned on login/register and sent in `Authorization` header.
- `authMiddleware.js` must verify token and attach user object to `req.user`.
- `rateLimiter.js` protects against brute force/API abuse.
- Input validation: prefer express-validator or Joi for robust validation.
- Passwords must be hashed (bcrypt). Never store plaintext.

## Testing & Postman

- Add a Postman collection / OpenAPI spec to document all endpoints if you plan to onboard others.
- Unit tests: add tests for controllers and services. Start with auth, loan creation, and payment flows.

## Deployment notes

- The repository includes `Dockerfile` and `docker-compose.yml`. For production, ensure environment variables are set in your hosting provider (Render, Heroku, or VPS) and secrets are stored securely.
- If integrating with blockchain, use a dedicated provider (Infura/Alchemy) and never commit private keys. Use environment-managed secrets.

## Contributing

1. Fork repo
2. Create feature branch
3. Make changes and add tests
4. Open a PR

Follow code style and run lint/tests before opening the PR.

## Next steps (recommended)

- Generate an OpenAPI (Swagger) spec from controllers.
- Create a Postman collection and add example requests/responses.
- Add an integration test that spins a test DB and runs key flows (register -> create loan -> fund -> payment)
- Secure blockchain private key usage via an HSM or managed secret store.

---

If you'd like, I can now:
- generate an OpenAPI spec for the current routes by scanning `routes/` and `controllers/`,
- or create a Postman collection with sample requests for every endpoint,
- or scaffold the frontend pages in `client/` for the pages described above.

Tell me which of those you'd like next and I will proceed.

#User Flow and Architecture
https://www.canva.com/design/DAGxJ5QVcsw/MoqUfL5WccXMicNbGCjSvg/edit?utm_content=DAGxJ5QVcsw&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton
#Figma Link
https://www.figma.com/design/H0oulDsdJcLner3NoDtcCc/FinTrustChain?node-id=303-22&t=axb9ylmHpqbysmS8-1
