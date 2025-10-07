# 🪙 Solana Token Tutorial — Create Your Own Token (2025)

This guide walks you through creating and managing your own token on the Solana blockchain using the latest CLI tools (2025 versions).  
All steps are compatible with **Windows (via WSL)**, **macOS**, and **Linux**.

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

## 1. Install Dependencies (Official Solana Installer)

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

## 2. Configure Solana for Devnet

Switch to the **Devnet** (Solana’s free public test network):

```bash
solana config set --url https://api.devnet.solana.com
solana config get
```

---

## 3. Create and Fund Your Wallet

Generate a new wallet (keypair) and airdrop 2 test SOL:

```bash
solana-keygen new --outfile ~/.config/solana/devnet.json
solana config set --keypair ~/.config/solana/devnet.json
solana airdrop 2
solana balance
```

Save your **12-word recovery phrase** safely — it can restore your wallet later.

---

## 4. Create Your Token Mint

Create your new token mint:

```bash
spl-token create-token
```

This returns your token’s **mint address** (its on-chain ID).  
Example:
```
Token: 8H5y3xJ7R6Qye4nTn6C2W5ZQAvTpd9RVkX2wZCPLM5wE
```

---

## 5. Create a Token Account

Each wallet needs a token account to hold tokens:

```bash
spl-token create-account <TOKEN_MINT_ADDRESS>
```

---

## 6. Mint Tokens

Mint one million tokens to your wallet:

```bash
spl-token mint <TOKEN_MINT_ADDRESS> 1000000
spl-token balance <TOKEN_MINT_ADDRESS>
```

---

## 7. Transfer, Burn, or Manage Supply

### (A) Create a Second Wallet
```bash
solana-keygen new --outfile ~/.config/solana/second.json
```

### (B) Fund the New Wallet
```bash
solana airdrop 1 --keypair ~/.config/solana/second.json
```

### (C) Get the Public Address
```bash
solana-keygen pubkey ~/.config/solana/second.json
```

### (D) Transfer Tokens
```bash
spl-token transfer <TOKEN_MINT_ADDRESS> 100 <RECIPIENT_PUBKEY> --fund-recipient
```

### (E) Burn Tokens (Optional)
```bash
spl-token burn <TOKEN_MINT_ADDRESS> 50
```

---

## 8. View Your Token on Solana Explorer

Visit:

🔗 [https://explorer.solana.com/?cluster=devnet](https://explorer.solana.com/?cluster=devnet)

Paste your token’s **mint address** to view its on-chain details.

---

## 9. Add Your Token to Phantom Wallet

### (A) Install Phantom
Download from [https://phantom.app](https://phantom.app) and install the browser extension (Chrome, Brave, or Firefox).

### (B) Import Your CLI Wallet
1. Open Phantom → **Import an existing wallet**
2. Enter the **12-word recovery phrase** from `solana-keygen new`
3. Set a password and finish setup

> 💡 This connects Phantom to the same wallet created in the CLI.

You can recover the phrase again later using:
```bash
solana-keygen recover
```

### (C) Switch Phantom to Devnet
1. Go to **Settings → Developer Settings → Enable Test Networks**
2. Then **Settings → Change Network → Devnet**

### (D) Add Your Token Manually
1. Go to **Settings → Manage Token List → Custom Token**
2. Paste your token’s **mint address**
3. Click **Save**

Your new token should now appear in Phantom with its balance.

---

## 10. (Optional) Add Metadata with Metaplex

Attach a name, symbol, and image to your token:

```bash
metaplex create_metadata   --name "MyToken"   --symbol "MYT"   --uri "https://mytoken.com/metadata.json"   --mint <YOUR_TOKEN_MINT_ADDRESS>
```

Example metadata JSON:
```json
{
  "name": "MyToken",
  "symbol": "MYT",
  "description": "A demo token built on Solana.",
  "image": "https://mytoken.com/logo.png"
}
```

> Only the **update authority** (you) can modify metadata.  
> To lock it permanently:
```bash
metaplex update_metadata --mint <YOUR_TOKEN_MINT_ADDRESS> --is_mutable false
```

---

## 11. (Optional) Make It Tradable

To list your token on Solana DEXs (like **Raydium** or **Jupiter**), create a liquidity pool through their UIs or SDKs.  
That’s an advanced step — optional for this basic token setup.

---

## ✅ Quick Reference Summary

| Step | Command | Purpose |
|------|----------|----------|
| Pre | `wsl --install` | Set up WSL on Windows |
| 1 | Solana installer | Install all dependencies |
| 2 | `solana config set --url devnet` | Switch to Devnet |
| 3 | `solana-keygen new` | Create wallet |
| 4 | `spl-token create-token` | Create token mint |
| 5 | `spl-token create-account` | Create token account |
| 6 | `spl-token mint` | Mint supply |
| 7 | `spl-token transfer` | Manage or burn tokens |
| 8 | Explorer | Verify token |
| 9 | Phantom | Import wallet + view token |
| 10 | Metaplex | Add metadata |
| 11 | DEX | Optional trading setup |

---

### 🧩 Troubleshooting Tips

- If you get `command not found` for `solana`, run:
  ```bash
  source ~/.bashrc
  ```
- To check your wallet address:
  ```bash
  solana address
  ```
- To view all token accounts:
  ```bash
  spl-token accounts
  ```
- To reset configuration:
  ```bash
  solana config set --url https://api.devnet.solana.com
  ```

---

**Author:** BlockExplorer 
**Date:** 2025  
**Version:** Solana CLI 2.2.12
