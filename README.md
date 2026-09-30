# Solid Explorer `.sec` Decryptor

A single-page, **100% client-side** tool to decrypt files encrypted by
[Solid Explorer](https://neatbytes.com/solidexplorer/) for Android. Files and
passwords **never leave your browser** — it works offline.

> Unofficial. Not affiliated with NeatBytes / Solid Explorer.

![screenshot](docs/screenshot.png)

## Live demo

After deploying to GitHub Pages (below): `https://<your-username>.github.io/<repo>/`

## Deploy to GitHub Pages

1. Create a repo and add these files to its root.
2. Push to GitHub.
3. **Settings → Pages → Build and deployment**:
   - Source: **Deploy from a branch**
   - Branch: **main**, folder **/ (root)** → **Save**.
4. Wait ~1 minute → your page is live. It's a static file, no build step.

### Add a "View source" link (recommended)
In `index.html`, near the bottom of the `<script>`, set your repo URL:
```js
const REPO='https://github.com/<you>/<repo>';
```
For a tool people trust with passwords, visible source builds trust.

## How it works

Solid Explorer's single-file format:
```
[ salt: 16 ][ IV: 16 ][ verifier: 4 ][ ciphertext ... ]
```
- **Key:** `PBKDF2(password, salt)` — HMAC-SHA256 ×100001 (modern) or HMAC-SHA1 ×1024 (legacy). Both are tried automatically.
- **Cipher:** AES-256-CTR (counter = IV as a 128-bit big-endian integer, +1 per 16-byte block).
- **Verifier:** the constant bytes `04 0A FE 37` encrypted with the key/IV — used to detect a wrong password instead of producing garbage.
- Output filename = input with the trailing `.sec` removed.
- No compression; ciphertext length equals the original file length.

Only **single-file** encryption is supported. Encrypted *folders* (which also
scramble filenames) are not handled yet.

## Limits

- Decryption happens in memory, so very large files (multi-GB) are constrained by
  browser RAM. A streaming desktop build is better for those.
- Requires a browser with the Web Crypto API (all current browsers).

## Security

- Everything is computed locally via the browser's Web Crypto. Nothing is uploaded.
- No dependencies, no analytics, no network calls.

## License

[MIT](LICENSE). Do not bundle any Solid Explorer code or assets.
