# 🪙 Solana Token Tutorial — Token‑2022 (2025 Edition)

A modern, streamlined workflow for creating and testing your own Solana token using the **Token‑2022 program** with built‑in metadata extensions.  
All steps are tested and work on **Windows (via WSL)**, **macOS**, and **Linux** using **Devnet**.

---

## Step 1. Windows (WSL) Setup

If you're on Windows, open **PowerShell (Run as Administrator)** and run:

```bash
wsl --install
```

Restart, open your Ubuntu WSL terminal, and complete all remaining steps there.

---

## Step 2. Install Solana & Dependencies

Install Solana CLI and all required tools with the official installer:

```bash
curl --proto '=https' --tlsv1.2 -sSfL https://solana-install.solana.workers.dev | bash
```

When complete, verify your environment:

```bash
rustc --version && solana --version && node --version
```

---

## Step 3. Configure Solana for Devnet

```bash
solana config set --url https://api.devnet.solana.com
solana config get
```

---

## Step 4. Create & Fund a Wallet

```bash
solana-keygen new --outfile ~/.config/solana/devnet.json
solana config set --keypair ~/.config/solana/devnet.json
solana airdrop 2
solana balance
```

Save your 12‑word recovery phrase securely.

---

## Step 5. Why Token‑2022?

Token‑2022 introduces native **metadata extensions** that let you store the token’s `name`, `symbol`, and `uri` **directly on the mint account** — no Metaplex metadata program required.  
This is now the standard approach and supported by all major wallets and explorers.

---

## Step 6. Create a Token‑2022 Mint

Use the **Token‑2022 program ID** and enable metadata at creation:

```bash
spl-token create-token   --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb   --enable-metadata --decimals 9
```

The output will include your **MINT_ADDRESS**. Copy it.

---

## Step 7. Prepare Metadata & Logo (IPFS / Pinata)

Create a folder with your metadata and logo:

```
mytoken/
 ├─ metadata.json
 └─ mytoken-logo.png
```

**metadata.json:**

```json
{
  "name": "MyToken Token",
  "symbol": "MYT",
  "description": "The official utility token of MyToken.",
  "image": "mytoken-logo.png",
  "external_url": "https://mytoken.io",
  "properties": {
    "files": [{ "uri": "mytoken-logo.png", "type": "image/png" }],
    "category": "image"
  }
}
```

Then upload the **entire folder** to [Pinata](https://pinata.cloud) → *Upload Folder*.

You’ll get a **folder CID**, such as `bafy...`.  
Your files will be reachable at:

```
https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json
https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/mytoken-logo.png
```

That metadata URL will be linked to the mint in the next step.

---

## Step 8. Initialize On‑Mint Metadata

Attach your token’s name, symbol, and metadata URI directly on the mint:

```bash
spl-token initialize-metadata <MINT_ADDRESS>   "MyToken Token" "MYT"   "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json"
```

Verify:

```bash
spl-token show <MINT_ADDRESS>
```

You should see the metadata fields populated.

---

## Step 9. Create an Associated Account & Mint Tokens

```bash
spl-token create-account <MINT_ADDRESS>
spl-token mint <MINT_ADDRESS> 1000000
spl-token balance <MINT_ADDRESS>
```

---

## Step 10. View in Phantom Wallet

1. Open Phantom → Settings → Developer Settings → Enable *Test Networks* → switch to **Devnet**.  
2. Import your CLI wallet using the 12‑word phrase.  
3. Your token should appear automatically.  
   If not, add it manually under **Manage Token List → Custom Token** and paste your `<MINT_ADDRESS>`.

---

## Step 11. Update Metadata (Optional)

```bash
spl-token update-metadata <MINT_ADDRESS> uri "https://gateway.pinata.cloud/ipfs/<NEW_CID>/metadata.json"
spl-token update-metadata <MINT_ADDRESS> name "New Token Name"
spl-token update-metadata <MINT_ADDRESS> symbol "NEW"
```

---

## Step 12. Legacy Tokens (Tokenkeg)

If your token was created under the older Tokenkeg program, use the **Metaplex Token Metadata** program (via **Metaboss** or SDK) to attach metadata.  
Token‑2022 metadata cannot be retrofitted onto older mints.

---

## Verify in Explorer

Open:
```
https://explorer.solana.com/address/<MINT_ADDRESS>?cluster=devnet
```
You should see your token name, symbol, and logo.

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Image not showing in Phantom | Use absolute `ipfs://<CID>/file.png` inside your metadata JSON |
| Metadata not updating | Ensure you’re signing with the mint authority |
| Commands missing | Reload PATH: `source ~/.bashrc` |

---

## 🧩 Quick Commands Summary

```
# Create Token-2022 mint
spl-token create-token   --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb   --enable-metadata --decimals 9

# Upload metadata + image folder to Pinata → get <FOLDER_CID>

# Initialize metadata on the mint
spl-token initialize-metadata <MINT_ADDRESS>   "MyToken Token" "MYT"   "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json"

# Create account + mint supply
spl-token create-account <MINT_ADDRESS>
spl-token mint <MINT_ADDRESS> 1000000
```

---

**Author:** BlockExplorer  
**Date:** October 2025
