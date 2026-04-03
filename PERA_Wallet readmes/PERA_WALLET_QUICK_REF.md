# Pera Wallet Quick Reference

## 🚀 Quick Start (3 Steps)

1. **Install Pera Wallet**
   - Mobile: [iOS](https://apps.apple.com/app/id1459898525) | [Android](https://play.google.com/store/apps/details?id=com.algorand.android)
   - Browser: [Chrome Extension](https://chrome.google.com/webstore/detail/pera-wallet/)

2. **Get Testnet ALGO**
   - Switch to Testnet in Pera Wallet settings
   - Visit: https://bank.testnet.algorand.network/
   - Paste your address, get free ALGO

3. **Connect to CfoE**
   - Open http://127.0.0.1:8000
   - Click "Connect Pera Wallet"
   - Scan QR or approve popup
   - ✅ Done!

---

## 📱 Connection Methods

### Mobile App
1. Click "Connect Pera Wallet"
2. QR code appears
3. Open Pera Wallet app
4. Tap "Scan QR" in WalletConnect section
5. Approve connection

### Browser Extension
1. Click "Connect Pera Wallet"
2. Pera extension popup appears
3. Click "Connect"
4. ✅ Connected

---

## 🔍 What You'll See

**Before Connection:**
```
Wallet: Not connected
[Connect Pera Wallet]
```

**After Connection:**
```
Wallet: 🟢 ABC123...XYZ789
[Disconnect]

Status: Connected
Network: Algorand Testnet
Balance: 10.000000 ALGO
```

---

## ✅ Benefits vs .env Setup

| Feature | Pera Wallet | .env File |
|---------|-------------|-----------|
| Setup | 30 sec | 5 min |
| Security | Keys in wallet | Keys in file |
| Multi-user | ✅ Each user | ❌ Shared |
| UX | Click + scan | Copy-paste |

---

## 🛠️ Troubleshooting

**QR doesn't appear?**
→ Check browser console, refresh page

**Can't scan QR?**
→ Ensure Pera is on Testnet, not Mainnet

**Transaction fails?**
→ Fund wallet at testnet dispenser

**Balance shows 0?**
→ Verify Testnet mode, check address

---

## 🔐 Security Tips

✅ Only use Testnet for demos  
✅ Never share private keys  
✅ Review transactions before signing  
✅ Disconnect when done  

---

## 📚 Resources

- **Pera Wallet:** https://perawallet.app/
- **Testnet Dispenser:** https://bank.testnet.algorand.network/
- **AlgoExplorer:** https://testnet.algoexplorer.io/
- **Full Guide:** See `PERA_WALLET_GUIDE.md`

---

## 🎯 Common Use Cases

**Demo to stakeholders:**
1. Connect your wallet
2. Run audit
3. Show on-chain TX on AlgoExplorer

**Multi-user testing:**
1. User A connects wallet A
2. Runs audit
3. User B connects wallet B
4. Runs audit
5. Compare results

**HITL workflow:**
1. Submit critical risk audit
2. Approve with connected wallet
3. View HITL TX on blockchain

---

**Need help?** See full guide in `PERA_WALLET_GUIDE.md`
