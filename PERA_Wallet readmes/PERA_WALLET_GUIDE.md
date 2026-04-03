# Pera Wallet Browser Integration Guide

## Overview

CfoE now supports **browser-based Pera Wallet connection** using WalletConnect. Users can connect their Algorand wallet directly from the web UI without manual `.env` configuration.

## Features

✅ **No Manual Configuration** - Connect wallet with QR code or deep link  
✅ **Session Persistence** - Wallet stays connected across page refreshes  
✅ **Real-time Balance** - Live ALGO balance display  
✅ **Transaction Signing** - Sign audit transactions directly from browser  
✅ **Multi-Wallet Support** - Works with Pera Wallet mobile and browser extension  

---

## Quick Start

### 1. Install Pera Wallet

**Mobile App:**
- iOS: [Download from App Store](https://apps.apple.com/app/id1459898525)
- Android: [Download from Google Play](https://play.google.com/store/apps/details?id=com.algorand.android)

**Browser Extension:**
- Chrome/Edge: [Install from Chrome Web Store](https://chrome.google.com/webstore/detail/pera-wallet/)
- Firefox: [Install from Firefox Add-ons](https://addons.mozilla.org/firefox/addon/pera-wallet/)

### 2. Setup Testnet Wallet

1. Open Pera Wallet
2. Create a new account or import existing
3. **Switch to Testnet** in Settings → Developer Settings → Node Settings → Testnet
4. Copy your wallet address
5. Get free testnet ALGO: https://bank.testnet.algorand.network/

### 3. Connect to CfoE

1. Start the CfoE web app:
   ```bash
   uvicorn webapp:app --reload
   ```

2. Open http://127.0.0.1:8000

3. Navigate to the **Blockchain Status** panel

4. Click **"Connect Pera Wallet"**

5. **Mobile:** Scan QR code with Pera Wallet app  
   **Browser Extension:** Approve connection in popup

6. Confirm connection in Pera Wallet

7. ✅ Your wallet is now connected!

---

## Usage

### Viewing Wallet Status

The **Blockchain Status** panel shows:
- 🟢 Connection status (Connected/Not connected)
- Wallet address (shortened: `ABC123...XYZ789`)
- ALGO balance
- Network (Algorand Testnet)
- Transaction counts

### Running Audits with Connected Wallet

Once connected, all audit transactions will:
1. Use your connected wallet address
2. Be signed by your wallet (you'll see approval prompts)
3. Be recorded on-chain with your wallet as the auditor
4. Show real-time transaction confirmations

### Disconnecting Wallet

Click **"Disconnect"** button to:
- End the WalletConnect session
- Revert to default `.env` wallet (if configured)
- Clear wallet address from UI

---

## Technical Details

### Architecture

```
┌─────────────────┐
│   Web Browser   │
│   (Frontend)    │
└────────┬────────┘
         │ WalletConnect Protocol
         │
┌────────▼────────┐
│  Pera Wallet    │
│  (Mobile/Ext)   │
└────────┬────────┘
         │ Sign Transactions
         │
┌────────▼────────┐
│ Algorand Testnet│
│   (Blockchain)  │
└─────────────────┘
```

### WalletConnect Flow

1. **Initialization**: Frontend loads Pera Wallet SDK from CDN
2. **Connection**: User scans QR or approves browser popup
3. **Session**: WalletConnect establishes encrypted session
4. **Backend Sync**: Frontend notifies backend of connected address
5. **Transactions**: Backend creates unsigned txns, frontend signs via wallet
6. **Confirmation**: Signed txns submitted to Algorand network

### API Endpoints

**GET /api/wallet/status**
```json
{
  "connected": true,
  "address": "4KG534Q6BUNUNDJRA7XBUH4OXYYXXHYFEAEHO3TASHU22WRD3AGGLGR2Y4",
  "method": "walletconnect"
}
```

**POST /api/wallet/connect**
```json
{
  "address": "4KG534Q6BUNUNDJRA7XBUH4OXYYXXHYFEAEHO3TASHU22WRD3AGGLGR2Y4"
}
```

**POST /api/wallet/disconnect**
```json
{
  "status": "disconnected"
}
```

---

## Troubleshooting

### Connection Issues

**Problem:** QR code doesn't appear  
**Solution:** Check browser console for errors, ensure Pera Wallet SDK loaded

**Problem:** Mobile app doesn't scan QR  
**Solution:** Ensure Pera Wallet is on Testnet, not Mainnet

**Problem:** Browser extension doesn't popup  
**Solution:** Check if extension is enabled, refresh page and retry

### Transaction Failures

**Problem:** "Insufficient balance" error  
**Solution:** Fund your testnet wallet at https://bank.testnet.algorand.network/

**Problem:** Transaction timeout  
**Solution:** Testnet can be slow, wait 10-15 seconds and retry

**Problem:** "Invalid signature" error  
**Solution:** Disconnect and reconnect wallet, ensure correct network

### Session Issues

**Problem:** Wallet disconnects on page refresh  
**Solution:** This is expected if session expired, reconnect manually

**Problem:** Balance shows 0 but wallet has funds  
**Solution:** Ensure wallet is on Testnet, check address matches

---

## Security Best Practices

### For Users

✅ **Only connect on Testnet** - Never use mainnet wallet for testing  
✅ **Verify URLs** - Ensure you're on `localhost:8000` or trusted domain  
✅ **Review transactions** - Always check transaction details before signing  
✅ **Disconnect when done** - End session after testing  

### For Developers

✅ **Never log private keys** - WalletConnect handles signing securely  
✅ **Validate addresses** - Check address format before backend operations  
✅ **Handle errors gracefully** - User may reject transaction signing  
✅ **Use testnet only** - Production requires proper security audit  

---

## Comparison: Browser vs .env Configuration

| Feature | Browser (WalletConnect) | .env Configuration |
|---------|------------------------|-------------------|
| Setup Time | 30 seconds | 5 minutes |
| User Experience | Click + Scan QR | Manual copy-paste |
| Security | Wallet holds keys | Keys in .env file |
| Multi-User | Each user their wallet | Shared wallet |
| Transaction Signing | User approves each | Automatic |
| Best For | Interactive demos | Automated scripts |

---

## Advanced: Custom Network Configuration

### Using Mainnet (Production)

⚠️ **WARNING:** Only use mainnet with proper security review

1. Change Pera Wallet to Mainnet
2. Update `blockchain_client.py`:
   ```python
   self.algod_server = "https://mainnet-api.algonode.cloud"
   ```
3. Ensure wallet has real ALGO for transaction fees
4. Test thoroughly on testnet first

### Using Local AlgoKit Node

1. Start local node:
   ```bash
   algokit localnet start
   ```

2. Update Pera Wallet custom node:
   - Server: `http://localhost:4001`
   - Token: `aaaa...` (64 'a's)

3. Fund account from local dispenser

---

## FAQ

**Q: Can I use MetaMask instead?**  
A: No, MetaMask is for Ethereum. CfoE uses Algorand blockchain. Use Pera Wallet, MyAlgo, or Defly Wallet.

**Q: Do I need real ALGO?**  
A: No, testnet ALGO is free from the dispenser. Never use real funds for testing.

**Q: Can multiple users connect different wallets?**  
A: Yes, each browser session can connect a different wallet. Perfect for multi-user demos.

**Q: What happens if I reject a transaction?**  
A: The audit will fail gracefully with an error message. You can retry.

**Q: Is my private key sent to the server?**  
A: No, WalletConnect keeps keys in your wallet. Server only receives signed transactions.

**Q: Can I switch wallets without refreshing?**  
A: Yes, disconnect current wallet and connect a different one.

---

## Support

- **Pera Wallet Docs:** https://docs.perawallet.app/
- **WalletConnect Docs:** https://docs.walletconnect.com/
- **Algorand Testnet:** https://testnet.algoexplorer.io/
- **CfoE Issues:** Open issue on GitHub repository

---

## Next Steps

1. ✅ Connect your Pera Wallet
2. ✅ Run a test audit
3. ✅ View transaction on AlgoExplorer
4. ✅ Try HITL approval workflow
5. ✅ Compare multiple audits

**Happy auditing! 🚀**
