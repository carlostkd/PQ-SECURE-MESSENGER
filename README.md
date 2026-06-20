# PQ | Secure | Messenger

A zero-knowledge, post-quantum encrypted messaging platform. Messages are encrypted and decrypted entirely in your browser using **ML-KEM-768** (NIST-standardized post-quantum key encapsulation), **HKDF-SHA256**, and **AES-256-GCM**. The server never sees your keys or plaintext.

## Features

### Encryption & Sharing

- **Text messages** — Write a secret message, get a share link. The secret key is embedded in the URL. No pre-shared keys needed, no accounts required.
- **File uploads** — Encrypt files up to 10 MB. Files are password-protected with PBKDF2 (100k iterations) + AES-256-GCM.
- **Combined text + file** — Send a message alongside a file in a single encrypted payload.
- **Password protection** — Optionally protect text messages with a password (requires 8+ chars, letters, numbers, and 2+ special chars).
- **View limits** — Set a max view count (1–100). Messages self-destruct after the limit is reached.
- **Auto-expiry** — All messages are automatically deleted after 7 days.
- **Short links** — Create short, shareable URLs (e.g., `s.php?c=abc123`).

### Replies

- Messages without a view limit can have replies enabled.
- Recipients can reply using the sender's public key — all replies are end-to-end encrypted.
- Reply keys are embedded in the share URL (`rk` parameter).

### Persistent Key Pairs

- Optionally generate a persistent ML-KEM-768 key pair.
- Share your public key so others can encrypt messages to you without a one-time keypair.
- Secret key is never stored on the server  save it offline.

### Dashboard (Registered Users)

- **Account required** — One-time payment of 5 CHF via Stripe.
- **Message tracking** — View all your messages, their status, view counts, reply counts, and timestamps.
- **Link management** — Copy the full share link (including the secret key) directly from the dashboard. Short links are displayed when available.
- **Pause/Unpause** — Temporarily disable access to a message without deleting it.
- **Delete** — Remove messages permanently (API-level authentication enforced).

### Two-Factor Authentication (2FA)

- **TOTP-based** — Compatible with any authenticator app, Proton PASS recomended.
- **Recovery codes** — 8 one-time recovery codes generated during setup. 


### Decryption Experience

- **Auto-fill from URL** — When opening a share link (`view.php?id=...&k=...`), the key is auto-filled and data is pre-fetched.
- **Missing key fallback** — If the URL has no key (`?id=...` without `&k=`), a prompt asks you to paste the key manually.
- **View indicator** — Shows remaining views (e.g., "2 of 5 views remaining").
- **Self-destruct notice** — Alerts when the message has hit its view limit.
- **Reply preview** — If you have the reply key (`rk`), replies are decrypted and shown before you open the main message.
- **Copy, Refresh, and Back buttons** — Copy decrypted text, refresh the message, or navigate home.
- **File download** — Decrypted files trigger a direct download.

## Cryptographic Chain

```
ML-KEM-768  →  HKDF-SHA256  →  AES-256-GCM
```

1. **ML-KEM-768** — Post-quantum key encapsulation. Generates a shared secret that only the intended recipient can recover.
2. **HKDF-SHA256** — Derives a strong symmetric key from the shared secret with domain separation.
3. **AES-256-GCM** — Authenticated encryption of the actual message data.

## Zero-Knowledge Architecture

- Key generation, encryption, and decryption all happen in your browser.
- Cryptographic operations use the Web Crypto API and WebAssembly (liboqs).
- The server stores only ciphertext  data computationally indistinguishable from random noise.
- No third-party scripts, no CDN, no tracking.

## Technical Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Vanilla JS, Web Crypto API, WebAssembly (liboqs) |
| Backend | PHP  (ciphertext storage only  no server-side crypto) |
| 2FA | TOTP (RFC 6238), SHA1, 30s period, 3-step window |



