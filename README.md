# 🪙 Solana Token Tutorial — Token-2022 (2025 Edition)

A modern, streamlined workflow for creating and testing your own Solana token using the **Token-2022** program with built-in metadata extensions.

All steps are tested and work on **Windows (via WSL)**, **macOS**, and **Linux** using **Devnet**.

---

## 🧩 Step-by-Step Tutorial

### 1️⃣ Install Solana CLI
Use the official installer:

```bash
sh -c "$(curl -sSfL https://release.solana.com/stable/install)"
```

Confirm installation:
```bash
solana --version
```

Switch to Devnet:
```bash
solana config set --url devnet
```

---

### 2️⃣ Create a Wallet
```bash
solana-keygen new
```

This will create your wallet and output your **public key** — save it somewhere safe.

Check your config:
```bash
solana config get
```

Request some Devnet SOL for testing:
```bash
solana airdrop 2
```

---

### 3️⃣ Create a Token Mint (Token-2022)

Token-2022 allows on-chain metadata, decimals configuration, and future-proof extensions.

```bash
spl-token create-token --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb --enable-metadata --decimals 9
```

Copy the **mint address** from the output.

---

### 4️⃣ Create Token Account
```bash
spl-token create-account <MINT_ADDRESS>
```

This creates a wallet account capable of holding your new token.

---

### 5️⃣ Mint Token Supply
```bash
spl-token mint <MINT_ADDRESS> 1000000
```

This mints **1,000,000 tokens** to your account.

Check balances:
```bash
spl-token balance <MINT_ADDRESS>
```

---

### 6️⃣ Add Metadata (Image + Description)

#### 📁 Folder Setup
Create a local folder, e.g. `metadata/`, with two files:

```
metadata/
 ├── mytoken-logo.png
 └── metadata.json
```

**metadata.json** example:

```json
{
  "name": "MyToken Token",
  "symbol": "MTK",
  "description": "The official utility token of the MyToken project — an example Solana token used for this tutorial.",
  "image": "mytoken-logo.png",
  "external_url": "https://mytoken.io",
  "attributes": [
    { "trait_type": "Category", "value": "Utility" },
    { "trait_type": "Network", "value": "Solana Devnet" }
  ],
  "properties": {
    "files": [{ "uri": "mytoken-logo.png", "type": "image/png" }],
    "category": "image",
    "creators": [
      { "address": "<YOUR_WALLET_ADDRESS>", "share": 100 }
    ]
  }
}
```

---

### 7️⃣ Upload to IPFS (via Pinata)

1. Upload the entire `metadata` folder to Pinata.  
2. Copy the folder’s CID (e.g. `bafybeihabc123...`).  
3. Your JSON file will now be hosted at:

```
https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json
```

and the image at:

```
https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/mytoken-logo.png
```

---

### 8️⃣ Attach Metadata to the Token
```bash
spl-token initialize-metadata <MINT_ADDRESS> "MyToken Token" "MTK" "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json"
```

This associates your metadata with your Token-2022 mint.

---

### 9️⃣ Verify in Solana Explorer
Visit:

```
https://explorer.solana.com/address/<MINT_ADDRESS>?cluster=devnet
```

Your token should now display with its **name**, **symbol**, and **image**.

---

### 🔟 Transfer Tokens
```bash
spl-token transfer <MINT_ADDRESS> 100 <RECIPIENT_ADDRESS>
```

This sends 100 tokens to another wallet.

---

## 🧭 Quick Recap
For reference, here’s the full command sequence:

---

## ✅ Quick Reference Summary

| Step | Command | Purpose |
|------|----------|----------|
| Pre | `wsl --install` | Set up WSL on Windows |
| 1 | Solana installer | Install all dependencies |
| 2 | `solana config set --url devnet` | Switch to Devnet |
| 3 | `solana-keygen new` | Create wallet |
| 4 | `spl-token create-token --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb --enable-metadata --decimals 9` | Create Token-2022 mint |
| 5 | `spl-token create-account <MINT_ADDRESS>` | Create token account |
| 6 | `spl-token mint <MINT_ADDRESS> 1000000` | Mint supply |
| 7 | `spl-token initialize-metadata <MINT_ADDRESS> "MyToken" "MTK" "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json"` | Attach metadata |
| 8 | `spl-token transfer <MINT_ADDRESS> 100 <RECIPIENT_ADDRESS>` | Transfer tokens |
| 9 | Explorer | Verify token in Explorer |

---

🧠 **Author:** BlockExplorer 
📅 **Updated:** October 2025  
📘 **Version:** Token-2022 Tutorial Edition
