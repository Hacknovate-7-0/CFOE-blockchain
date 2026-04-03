# Exodus Wallet Setup Guide for CfoE

## Quick Start (3 Steps)

### 1. Install Exodus Wallet Extension

**Install from Chrome Web Store:**
https://chromewebstore.google.com/detail/exodus-web3-wallet/

Or search "Exodus Wallet" in Chrome Web Store

### 2. Setup Testnet

1. Open Exodus Wallet extension
2. Create new wallet or import existing
3. Click Settings
4. Enable **Developer Mode**
5. Switch to **Algorand Testnet**
6. Copy your Algorand address

### 3. Get Testnet ALGO

1. Visit: https://bank.testnet.algorand.network/
2. Paste your Exodus wallet address
3. Click "Dispense"
4. Wait 10-15 seconds
5. Check balance in Exodus Wallet

---

## Connect to CfoE

1. Start CfoE:
   ```bash
   uvicorn webapp:app --reload
   ```

2. Open http://127.0.0.1:8000

3. In **Blockchain Status** panel:
   - Select "Exodus Wallet (Chrome - Recommended)"
   - Click "Connect"
   - Approve in Exodus Wallet popup

4. ✅ Connected! Your address shows in the panel

---

## Using Exodus Wallet

### Running Audits

1. Fill audit form
2. Click "Run Audit"
3. Exodus Wallet popup appears
4. Review transaction details
5. Click "Approve"
6. Transaction submitted to blockchain

### Viewing Transactions

**In Exodus Wallet:**
- Click "Activity" tab
- See all your transactions
- Click any TX for details

**On AlgoExplorer:**
- Copy TX ID from CfoE
- Visit: https://testnet.algoexplorer.io/
- Paste TX ID in search

---

## Troubleshooting

**Extension not detected?**
- Refresh the page
- Check extension is enabled
- Try restarting browser

**Connection fails?**
- Ensure Testnet selected in Exodus
- Check wallet is unlocked
- Try disconnecting and reconnecting

**Transaction fails?**
- Check you have testnet ALGO
- Verify network is Testnet
- Ensure sufficient balance (>0.1 ALGO)

---

## Why Exodus Wallet?

✅ **Chrome Extension** - No mobile app needed  
✅ **Multi-Chain** - Supports many cryptocurrencies  
✅ **User-Friendly** - Beautiful interface  
✅ **Testnet Support** - Easy network switching  
✅ **Free** - No cost to use  

---

## Resources

- **Exodus Wallet:** https://www.exodus.com/
- **Chrome Store:** Search "Exodus Wallet"
- **Testnet Dispenser:** https://bank.testnet.algorand.network/
- **AlgoExplorer:** https://testnet.algoexplorer.io/

---

**Ready to start!** 🚀
