# 🪙 Solana Token Tutorial — Token-2022 (2025 edition)

This README replaces the legacy flow and shows a modern, production-ready workflow (Oct 2025) using **Token-2022** metadata extensions so name / symbol / image live natively on the mint.  
All steps work on **Windows (WSL)**, **macOS**, and **Linux**. Use **Devnet** for testing.

---

## ⚙️ Pre-step: Windows (WSL)
If you're on Windows, open PowerShell **as Administrator** and run:
```bash
wsl --install
```
Restart, open your Ubuntu WSL terminal, and do the rest inside that terminal.

---

## 1. Install dependencies (official Solana installer)
```bash
curl --proto '=https' --tlsv1.2 -sSfL https://solana-install.solana.workers.dev | bash
```
Verify:
```bash
rustc --version && solana --version && node --version
```

---

## 2. Configure Solana for Devnet
```bash
solana config set --url https://api.devnet.solana.com
solana config get
```

---

## 3. Create and fund your wallet
```bash
solana-keygen new --outfile ~/.config/solana/devnet.json
solana config set --keypair ~/.config/solana/devnet.json
solana airdrop 2
solana balance
```
Save your 12-word recovery phrase securely.

---

## Why Token-2022?
Token-2022 lets you store `name`, `symbol`, and a `uri` **on the mint itself** via native token extensions — no separate Metaplex metadata account required for new mints. This is cleaner, supported by wallets, and recommended for new tokens in 2025.

---

## 4. Create a Token-2022 mint (with metadata enabled)
Use the **Token-2022 program id** and enable metadata at creation:
```bash
spl-token create-token   --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb   --enable-metadata --decimals 9
```
Output will include your **MINT_ADDRESS**. Copy it.

---

## 5. Prepare metadata JSON + logo (hosting on IPFS via Pinata)
Create a local folder:
```
mytoken/
  ├─ metadata.json
  └─ mytoken-logo.png
```

**Recommended metadata.json (for Pinata folder upload)**:
```json
{
  "name": "MyToken Token",
  "symbol": "MYT",
  "description": "The official utility token of MyToken.",
  "image": "mytoken-logo.png",
  "external_url": "https://mytoken.io",
  "properties": {
    "files": [
      { "uri": "mytoken-logo.png", "type": "image/png" }
    ],
    "category": "image"
  }
}
```
**Notes**:
- For a quick demo: upload the entire folder to Pinata (Upload Folder). This produces a **folder CID** and serves both files under that CID.
- For **wallet compatibility (recommended)**: after upload, **use the folder CID** to build an absolute `ipfs://` URI (see step 6) and **re-upload** metadata.json with the absolute `image` field if you want the JSON itself to reference `ipfs://...`. Many wallets (including Phantom on some clusters) prefer explicit `ipfs://<CID>/file.png` entries.

---

## 6. Upload to Pinata (or NFT.Storage)
1. Upload the whole `mytoken/` folder to Pinata.
2. Copy the **folder CID** from Pinata (example `bafy...`).
3. Your live URLs will be:
   - Metadata: `https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json`
   - Image: `https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/mytoken-logo.png`

**Optional** (make metadata JSON explicit for wallets):
- Edit your local `metadata.json` to set:
  ```json
  "image": "ipfs://<FOLDER_CID>/mytoken-logo.png"
  ```
- Upload only the updated `metadata.json` to Pinata (it will receive a new CID). Use that metadata URL in step 7.

---

## 7. Initialize on-mint metadata (attach name/symbol/URI to the mint)
Use the `spl-token` helper commands for Token-2022:

```bash
# Initialize metadata on the mint (sets name, symbol, and URI)
spl-token initialize-metadata <MINT_ADDRESS>   "MyToken Token" "MYT"   "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json"
```

**Verify**:
```bash
spl-token show <MINT_ADDRESS>
# or (if available)
spl-token get-metadata <MINT_ADDRESS>
```

Wallets and explorers that support Token-2022 should now display name, symbol, and image. Give ~30–90 seconds, then refresh Phantom/Explorer.

---

## 8. Create an associated token account and mint tokens
```bash
spl-token create-account <MINT_ADDRESS>
spl-token mint <MINT_ADDRESS> 1000000
spl-token balance <MINT_ADDRESS>
```

---

## 9. Add token to Phantom
- Install Phantom extension (https://phantom.app) if needed.
- Import your CLI wallet using the 12-word phrase or keyfile.
- Switch Phantom to **Devnet**: Settings → Developer Settings → Enable Test Networks → Change Network → Devnet.
- If token doesn't auto-appear, add it manually via **Manage Token List → Custom Token** and paste the `<MINT_ADDRESS>`.

---

## 10. Updating metadata later
To update the on-mint metadata:
```bash
spl-token update-metadata <MINT_ADDRESS> uri "https://gateway.pinata.cloud/ipfs/<NEW_FOLDER_CID>/metadata.json"
# or update name/symbol
spl-token update-metadata <MINT_ADDRESS> name "New Name"
spl-token update-metadata <MINT_ADDRESS> symbol "NEW"
```

---

## 11. If you already have a legacy Tokenkeg mint
If your mint was created under the classic Token program, you **cannot** retroactively add Token-2022 extensions. Use the Metaplex Token Metadata program (common options):

**Metaboss (simple CLI)**:
```bash
cargo install metaboss --locked

# create minimal on-chain metadata for existing mint
cat > onchain.json << 'JSON'
{ "name": "MyToken Token", "symbol": "MYT",
  "uri": "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json" }
JSON

metaboss create metadata -a <MINT_ADDRESS> -m ./onchain.json -k ~/.config/solana/devnet.json --rpc https://api.devnet.solana.com
```

Or programmatic: use Umi or Metaplex JS SDK to create the metadata account (advanced).

---

## 12. Verify in Explorer
Visit:
`https://explorer.solana.com/address/<MINT_ADDRESS>?cluster=devnet`

You should see name/symbol and image once the metadata is active.

---

## Troubleshooting (common issues)
- **Image not showing in Phantom**: Ensure metadata JSON uses absolute `ipfs://<CID>/file.png` or the metadata JSON URI uses a gateway URL that points to a JSON that itself references `ipfs://...`. Refresh Phantom.
- **`spl-token` commands missing**: Ensure the updated Solana/spl-token binaries are in your PATH. `source ~/.bashrc` if you just installed.
- **Metadata not writing**: Confirm you're signing with the mint authority (`solana address` matches the authority).

---

## TL;DR (quick commands)
```bash
# create token (Token-2022)
spl-token create-token --program-id TokenzQdB... --enable-metadata --decimals 9

# upload folder to Pinata -> get <FOLDER_CID>

# initialize metadata on-mint
spl-token initialize-metadata <MINT_ADDRESS> "MyToken Token" "MYT" "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json"

# create account + mint
spl-token create-account <MINT_ADDRESS>
spl-token mint <MINT_ADDRESS> 1000000
```

---

**Author:** BlockExplorer  
**Date:** 2025  
