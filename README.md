# 🪙 Solana Token Tutorial — Token-2022 (2025 Edition)

A modern, streamlined workflow for creating and testing your own Solana token using the **Token-2022** program with built-in metadata extensions.

All steps are tested and work on **Windows (via WSL)**, **macOS**, and **Linux** using **Devnet**.

---

## 🧩 Step-by-Step Tutorial

---

## ⚙️ Pre-Step: Windows Setup (WSL Requirement)

If you’re on Windows, Solana development requires the **Windows Subsystem for Linux (WSL)**.

Open **PowerShell as Administrator** and run:

```bash
wsl --install
```

Restart your PC when prompted, then open **Ubuntu** from the Start menu.  
You’ll be asked to create a Linux username and password — that’s your local user account.

> 💡 Once WSL is installed, all remaining steps are performed inside the **Ubuntu terminal**, not PowerShell.

---

### 1️⃣ Install Solana CLI & Dependencies (Official Solana Installer)

Run the official installer from the Solana documentation:

```bash
curl --proto '=https' --tlsv1.2 -sSfL https://solana-install.solana.workers.dev | bash
```

Example successful output:

```
Installed Versions:
Rust: rustc 1.86.0 (05f9846f8 2025-03-31)
Solana CLI: solana-cli 2.2.12 (src:0315eb6a; feat:1522022101, client:Agave)
Anchor CLI: anchor-cli 0.31.1
Node.js: v23.11.0
Yarn: 1.22.1
```

Verify installation:

```bash
rustc --version && solana --version && anchor --version && node --version && yarn --version
```

---

### 2️⃣ Create a Wallet

Switch to the **Devnet** (Solana’s free public test network):

```bash
solana config set --url devnet
```

Create a wallet:

```bash
solana-keygen new --outfile ~/.config/solana/devnet.json
```

This will create your wallet and output your **public key** — save it somewhere safe.

Set it as the active wallet for the Solana CLI:

```bash
solana config set --keypair ~/.config/solana/devnet.json
```

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

### 6️⃣ Verify in Solana Explorer

Visit:

```
https://explorer.solana.com/address/<MINT_ADDRESS>?cluster=devnet
```

Your token will not display with its **name**, **symbol**, and **image** since we haven't added metadata yet.

---

### 7️⃣ Add Metadata (Image + Description)

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

### 8️⃣ Upload to IPFS (via Pinata **or Storacha**)

You can host your token metadata on any IPFS gateway. Below are two simple options:

---

#### 🅰 Option 1 — Pinata (Recommended for Beginners)

1. Upload the entire `metadata` folder to **Pinata**.  
2. Copy the folder’s **CID** (e.g. `bafybeihabc123...`).  
3. Your JSON file will now be hosted at:

```
https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json
```

and the image at:

```
https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/mytoken-logo.png
```

---

#### 🅱 Option 2 — Storacha (Alternative Gateway)

If you prefer a decentralized, fast gateway alternative:

1. Visit [https://storacha.network](https://storacha.network)  
2. Click **Upload Folder** and select your `metadata` directory.  
3. After upload, Storacha will return a **CID** (e.g. `bafkreihxyz789...`).  
4. Your hosted files will be accessible at:

```
https://storacha.network/ipfs/<FOLDER_CID>/metadata.json
```

and

```
https://storacha.network/ipfs/<FOLDER_CID>/mytoken-logo.png
```

> 🧠 Tip: both Pinata and Storacha host the same IPFS content, so you can use **either gateway** URL interchangeably in the next step.

---

### ✅ Continue to Step 9

Once you have your folder’s CID from **either** service, proceed to attach it to your token:

```bash
spl-token initialize-metadata <MINT_ADDRESS> "MyToken Token" "MTK" "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json"
```

> Replace the URL with your chosen gateway (Pinata or Storacha).

---

### 9️⃣ Attach Metadata to the Token

```bash
spl-token initialize-metadata <MINT_ADDRESS> "MyToken Token" "MTK" "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json"
```

This associates your metadata with your Token-2022 mint.

---

### 🔟 Transfer Tokens

```bash
spl-token transfer <MINT_ADDRESS> 100 <RECIPIENT_ADDRESS>
```

This sends 100 tokens to another wallet.

---

### 1️⃣1️⃣ (Optional) Update Metadata Later

Need to change your token’s **name**, **symbol**, or **URI** after launch? Use the Token-2022 metadata update command.  
> 🔐 You must sign with the **current metadata update authority** set during initialization.

Update any subset of fields (include only what you want to change):

```bash
# Examples (pick what you need)
spl-token update-metadata <MINT_ADDRESS> --name "MyToken Pro"
spl-token update-metadata <MINT_ADDRESS> --symbol "MTKP"
spl-token update-metadata <MINT_ADDRESS> --uri "https://gateway.pinata.cloud/ipfs/<NEW_FOLDER_CID>/metadata.json"

# Or all at once:
spl-token update-metadata <MINT_ADDRESS>   --name "MyToken Pro"   --symbol "MTKP"   --uri "https://gateway.pinata.cloud/ipfs/<NEW_FOLDER_CID>/metadata.json"
```

Then refresh views:
- **Explorer:** reload the mint page.  
- **Wallets:** restart / clear cache (Phantom: Settings → Troubleshooting → *Clear cache*).

> ℹ️ Note: Phantom may still show “Unknown Token” if it can’t resolve your JSON via the Metaplex metadata path. The token will still display balances. Wallets like **Solflare** fully display Token-2022 metadata (name/logo).

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
| 6 | Explorer | Verify token in Explorer |
| 6.5 | `spl-token mint <MINT_ADDRESS> 1000000` | Mint supply |
| 7 | `spl-token initialize-metadata <MINT_ADDRESS> "MyToken" "MTK" "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json"` | Attach metadata |
| 8 | `spl-token transfer <MINT_ADDRESS> 100 <RECIPIENT_ADDRESS>` | Transfer tokens |
| 11 (opt) | `spl-token update-metadata <MINT_ADDRESS> --name/--symbol/--uri ...` | Update metadata later |

---

🧠 **Author:** BlockExplorer  
📅 **Updated:** October 2025  
📘 **Version:** Token-2022 Tutorial Edition
