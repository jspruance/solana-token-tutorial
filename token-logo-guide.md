# 🖼️ Solana Token Logo Guidelines (2025)

This document outlines best practices for creating and hosting a logo for your Solana token.  
Following these recommendations ensures your logo displays correctly in wallets like **Phantom**, **Solana Explorer**, and DEXs such as **Raydium** and **Jupiter**.

---

## 🪙 Recommended Logo Specifications

| Property | Recommended Value | Notes |
|-----------|------------------|-------|
| **Shape** | Square (1:1 aspect ratio) | Circular or rectangular images may be cropped |
| **Dimensions** | 512×512 px | Ideal size for most wallets |
| **Minimum Size** | 256×256 px | Acceptable for small displays |
| **Maximum Size** | 1024×1024 px | Larger images increase load times |
| **File Type** | PNG (preferred) | Supports transparency; JPG acceptable |
| **File Size** | ≤ 500 KB | Keep small for faster IPFS loading |
| **Background** | Transparent or dark-mode-friendly | Phantom uses dark UI themes |
| **Filename** | lowercase, no spaces (e.g., `mytoken-logo.png`) | Prevents IPFS link issues |

---

## 🎨 Design Tips

- Use **bold, simple shapes** — tokens are shown in small circles.  
- Avoid detailed text or small lettering.  
- Ensure **strong contrast** so the logo is visible at 32×32 px.  
- Save as **PNG-24** with transparency for best compatibility.  
- Test the logo at different sizes before uploading.

---

## 🧩 Integration with Metadata

Example entry inside your `metadata.json` file:

```json
{
  "name": "MyToken Coin",
  "symbol": "MYT"
  "description": "A Solana token for MyToken.",
  "image": "ipfs://QmXabc123/mytoken-logo.png",
  "properties": {
    "files": [
      { "uri": "ipfs://QmXabc123/mytoken-logo.png", "type": "image/png" }
    ],
    "category": "image"
  }
}
```

Wallets automatically display and resize the image when your token is viewed.

---

## ☁️ Hosting Recommendations

| Host Type | Examples | Notes |
|------------|-----------|-------|
| **IPFS (Recommended)** | Pinata, NFT.Storage, Web3.Storage | Decentralized, reliable, free tier |
| **Temporary (Testing)** | Imgur, GitHub Pages, Vercel | Use only for devnet/demo projects |

---

## ✅ TL;DR

- Use a **512×512 transparent PNG**, under **300–500 KB**.  
- Upload it alongside your `metadata.json` to **Pinata or NFT.Storage**.  
- Reference it via `ipfs://` or gateway URL in your metadata.  
- Keep it simple, centered, and high contrast for best appearance.

---

**Author:** MyToken
**Date:** 2025  
**Version:** 1.0  
