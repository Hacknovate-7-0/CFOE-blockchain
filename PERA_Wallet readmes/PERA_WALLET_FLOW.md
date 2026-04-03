# Pera Wallet Connection Flow

## Visual Connection Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                     PERA WALLET INTEGRATION                      │
└─────────────────────────────────────────────────────────────────┘

┌──────────────┐
│   User       │
│   Opens      │
│   CfoE UI    │
└──────┬───────┘
       │
       │ 1. Clicks "Connect Pera Wallet"
       │
       ▼
┌──────────────────────────────────────────────────────────────┐
│                    Frontend (Browser)                         │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  pera-wallet.js loads Pera Wallet Connect SDK          │  │
│  │  - Initializes WalletConnect session                   │  │
│  │  - Generates connection URI                            │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────┬───────────────────────────────────────┘
                       │
       ┌───────────────┴───────────────┐
       │                               │
       │ 2a. Mobile                    │ 2b. Browser Extension
       │     QR Code                   │     Popup
       │                               │
       ▼                               ▼
┌──────────────────┐          ┌──────────────────┐
│  Pera Wallet     │          │  Pera Wallet     │
│  Mobile App      │          │  Extension       │
│                  │          │                  │
│  User scans QR   │          │  User clicks     │
│  and approves    │          │  "Connect"       │
└────────┬─────────┘          └────────┬─────────┘
         │                             │
         │ 3. Wallet returns address   │
         │                             │
         └──────────────┬──────────────┘
                        │
                        ▼
         ┌──────────────────────────────┐
         │  WalletConnect Protocol      │
         │  - Encrypted session         │
         │  - Address shared            │
         │  - Ready for signing         │
         └──────────────┬───────────────┘
                        │
                        │ 4. Frontend receives address
                        │
                        ▼
         ┌──────────────────────────────────────────┐
         │  Frontend calls:                         │
         │  POST /api/wallet/connect                │
         │  { "address": "ABC123..." }              │
         └──────────────┬───────────────────────────┘
                        │
                        │ 5. Backend updates
                        │
                        ▼
         ┌──────────────────────────────────────────┐
         │  Backend (webapp.py)                     │
         │  - Updates blockchain_client.address     │
         │  - Stores in wallet_manager              │
         │  - Returns success                       │
         └──────────────┬───────────────────────────┘
                        │
                        │ 6. UI updates
                        │
                        ▼
         ┌──────────────────────────────────────────┐
         │  UI Shows:                               │
         │  🟢 Wallet: ABC123...XYZ789              │
         │  Balance: 10.000000 ALGO                 │
         │  [Disconnect]                            │
         └──────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                    AUDIT TRANSACTION FLOW                        │
└─────────────────────────────────────────────────────────────────┘

         ┌──────────────────────────────┐
         │  User submits audit          │
         └──────────────┬───────────────┘
                        │
                        │ 1. POST /api/audit
                        │
                        ▼
         ┌──────────────────────────────────────────┐
         │  Backend creates transaction             │
         │  - Uses connected wallet address         │
         │  - Calculates risk score                 │
         │  - Generates report                      │
         └──────────────┬───────────────────────────┘
                        │
                        │ 2. Unsigned transaction
                        │
                        ▼
         ┌──────────────────────────────────────────┐
         │  Frontend requests signature             │
         │  via Pera Wallet SDK                     │
         └──────────────┬───────────────────────────┘
                        │
                        │ 3. Sign request
                        │
                        ▼
         ┌──────────────────────────────────────────┐
         │  Pera Wallet shows transaction           │
         │  User reviews and approves               │
         └──────────────┬───────────────────────────┘
                        │
                        │ 4. Signed transaction
                        │
                        ▼
         ┌──────────────────────────────────────────┐
         │  Backend submits to Algorand             │
         │  - Transaction confirmed                 │
         │  - TX ID returned                        │
         └──────────────┬───────────────────────────┘
                        │
                        │ 5. Confirmation
                        │
                        ▼
         ┌──────────────────────────────────────────┐
         │  UI displays:                            │
         │  ✅ Audit complete                       │
         │  📜 TX: ABCD1234...                      │
         │  ⛓️ View on AlgoExplorer                │
         └──────────────────────────────────────────┘
```

---

## State Diagram

```
┌─────────────┐
│ Disconnected│
│   State     │
└──────┬──────┘
       │
       │ User clicks "Connect"
       │
       ▼
┌─────────────┐
│ Connecting  │
│   State     │
│ (QR shown)  │
└──────┬──────┘
       │
       │ User approves in wallet
       │
       ▼
┌─────────────┐
│  Connected  │◄────────┐
│   State     │         │
│ (Active)    │         │
└──────┬──────┘         │
       │                │
       │ User runs      │ Session
       │ audit          │ persists
       │                │
       ▼                │
┌─────────────┐         │
│ Transaction │         │
│  Signing    │─────────┘
│   State     │
└──────┬──────┘
       │
       │ User clicks "Disconnect"
       │
       ▼
┌─────────────┐
│ Disconnected│
│   State     │
└─────────────┘
```

---

## Component Interaction

```
┌────────────────────────────────────────────────────────────────┐
│                         Browser Layer                           │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐    │
│  │  index.html  │───▶│   app.js     │───▶│pera-wallet.js│    │
│  │              │    │              │    │              │    │
│  │ - UI buttons │    │ - Event      │    │ - SDK calls  │    │
│  │ - Status     │    │   handlers   │    │ - Session    │    │
│  │   display    │    │ - API calls  │    │   mgmt       │    │
│  └──────────────┘    └──────────────┘    └──────┬───────┘    │
│                                                   │             │
└───────────────────────────────────────────────────┼────────────┘
                                                    │
                                    ┌───────────────▼────────────┐
                                    │  WalletConnect Protocol    │
                                    │  (Encrypted Bridge)        │
                                    └───────────────┬────────────┘
                                                    │
┌───────────────────────────────────────────────────┼────────────┐
│                        Backend Layer              │             │
├───────────────────────────────────────────────────┼────────────┤
│                                                   │             │
│  ┌──────────────┐    ┌──────────────┐    ┌──────▼───────┐    │
│  │  webapp.py   │───▶│wallet_connect│───▶│blockchain_   │    │
│  │              │    │    .py       │    │  client.py   │    │
│  │ - API routes │    │              │    │              │    │
│  │ - Audit      │    │ - Session    │    │ - TX create  │    │
│  │   logic      │    │   state      │    │ - Signing    │    │
│  └──────────────┘    └──────────────┘    └──────┬───────┘    │
│                                                   │             │
└───────────────────────────────────────────────────┼────────────┘
                                                    │
                                    ┌───────────────▼────────────┐
                                    │   Algorand Testnet         │
                                    │   - Validates TX           │
                                    │   - Records on-chain       │
                                    └────────────────────────────┘
```

---

## Security Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                      SECURITY BOUNDARIES                         │
└─────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│  User's Device (Secure)                                       │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Pera Wallet                                           │  │
│  │  🔐 Private Key (NEVER leaves wallet)                  │  │
│  │  - Stored encrypted                                    │  │
│  │  - User password protected                             │  │
│  │  - Signs transactions locally                          │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
                              │
                              │ Only signed transactions
                              │ and public address shared
                              │
                              ▼
┌──────────────────────────────────────────────────────────────┐
│  CfoE Backend (Semi-Trusted)                                  │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  - Receives: Public address, signed transactions      │  │
│  │  - Never sees: Private keys, seed phrases             │  │
│  │  - Creates: Unsigned transactions                     │  │
│  │  - Submits: Signed transactions to blockchain         │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
                              │
                              │ Signed transactions only
                              │
                              ▼
┌──────────────────────────────────────────────────────────────┐
│  Algorand Blockchain (Public)                                 │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  - Validates signatures                                │  │
│  │  - Records transactions                                │  │
│  │  - Immutable audit trail                               │  │
│  │  - Publicly verifiable                                 │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘

🔒 Security Guarantee:
   Private keys NEVER transmitted over network
   Backend CANNOT sign transactions without user approval
   All transactions cryptographically verified on-chain
```

---

## Comparison: Connection Methods

```
┌─────────────────────────────────────────────────────────────────┐
│              BROWSER WALLET vs .ENV CONFIGURATION                │
└─────────────────────────────────────────────────────────────────┘

Browser Wallet (WalletConnect)          .env Configuration
─────────────────────────────────────────────────────────────────

Setup:                                   Setup:
┌──────────────┐                        ┌──────────────┐
│ 1. Click     │                        │ 1. Create    │
│    button    │                        │    wallet    │
│              │                        │              │
│ 2. Scan QR   │                        │ 2. Copy keys │
│              │                        │              │
│ 3. Done! ✅  │                        │ 3. Edit .env │
│              │                        │              │
│ Time: 30 sec │                        │ 4. Restart   │
└──────────────┘                        │              │
                                        │ Time: 5 min  │
                                        └──────────────┘

Security:                                Security:
┌──────────────┐                        ┌──────────────┐
│ Keys in      │                        │ Keys in      │
│ wallet app   │                        │ .env file    │
│              │                        │              │
│ User approves│                        │ Auto-sign    │
│ each TX      │                        │ all TX       │
│              │                        │              │
│ 🔐 Secure    │                        │ ⚠️  Risk if  │
│              │                        │    leaked    │
└──────────────┘                        └──────────────┘

Multi-User:                              Multi-User:
┌──────────────┐                        ┌──────────────┐
│ Each user    │                        │ All users    │
│ connects own │                        │ share same   │
│ wallet       │                        │ wallet       │
│              │                        │              │
│ ✅ Supported │                        │ ❌ Not ideal │
└──────────────┘                        └──────────────┘

Best For:                                Best For:
┌──────────────┐                        ┌──────────────┐
│ - Demos      │                        │ - Scripts    │
│ - Testing    │                        │ - Automation │
│ - Multi-user │                        │ - CI/CD      │
│ - Workshops  │                        │ - Batch jobs │
└──────────────┘                        └──────────────┘
```

---

## Quick Reference

**Connect:** Click "Connect Pera Wallet" → Scan QR or approve popup  
**Disconnect:** Click "Disconnect" button  
**Status:** Check "Blockchain Status" panel  
**Balance:** Displayed after connection  
**Transactions:** Automatically use connected wallet  

**Troubleshooting:**
- QR not showing? → Refresh page
- Can't scan? → Check Testnet mode
- TX fails? → Fund wallet at dispenser
- Balance 0? → Verify correct network

**Resources:**
- Full Guide: `PERA_WALLET_GUIDE.md`
- Quick Ref: `PERA_WALLET_QUICK_REF.md`
- Implementation: `PERA_WALLET_IMPLEMENTATION.md`
