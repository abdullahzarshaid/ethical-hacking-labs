# Module 20 — Cryptography

> *You won't break modern crypto — you'll break how people use it. That's where the bugs are.*

**The goal.** Understand the crypto you actually meet on engagements — hashing, symmetric/asymmetric encryption, TLS, digital signatures — and learn to spot and exploit *implementation* weaknesses: weak hashes, bad modes, hardcoded keys, and misconfigured TLS.

**Where it fits.** Crypto underpins every other module — password hashes (Module 06), TLS (Modules 08/13), tokens (Module 11), stored secrets (Modules 17–19). This module ties those threads together.

## Concepts that matter

- **Hashing ≠ encryption.** Hashes are one-way (integrity, password storage); encryption is reversible (confidentiality). Confusing them is a common flaw.
- **Password storage done right.** Slow, salted KDFs — **bcrypt, scrypt, Argon2** — not MD5/SHA-1. Fast hashes = fast cracking.
- **Symmetric vs asymmetric.** AES (shared key, fast) vs RSA/ECC (key pair, used for exchange/signatures). TLS uses both.
- **Where implementations fail.** ECB mode (patterns leak), hardcoded/reused keys, weak randomness, no integrity (padding-oracle), downgrade/weak-cipher TLS, expired/self-signed certs in production.

## Command cheat-sheet

```bash
# --- Identify & crack hashes ---
hashid '$2y$10$...'                         # identify the hash type
hash-identifier
john --format=raw-md5 hashes.txt --wordlist=rockyou.txt
hashcat -m 0 hashes.txt rockyou.txt         # MD5   (-m 100 SHA1, -m 1800 sha512crypt, -m 3200 bcrypt)

# --- Encoding is not encryption (recognise it) ---
echo "aGVsbG8=" | base64 -d                 # base64 decode
# CyberChef (browser) — the swiss-army knife for encoding/crypto ops

# --- TLS / certificate assessment ---
testssl.sh https://TARGET                   # protocols, ciphers, cert, known flaws
openssl s_client -connect TARGET:443 -showcerts
nmap --script ssl-enum-ciphers -p443 TARGET # weak cipher suites

# --- Symmetric crypto by hand (learn the modes) ---
openssl enc -aes-256-cbc -salt -in file -out file.enc     # note: needs integrity (GCM > CBC)
```

## Walk it in your lab

1. **Hash cracking:** create MD5 vs bcrypt hashes of the same password; crack both with `hashcat` and *time* them — feel why slow KDFs matter.
2. **ECB weakness:** encrypt a simple bitmap in AES-ECB and view it — the image is still visible. That's why mode choice matters.
3. **TLS audit:** run `testssl.sh` against a lab server; identify weak protocols/ciphers and fix the config.
4. **Encoding vs encryption:** decode base64/hex in CyberChef and internalise that encoding provides *no* confidentiality.

## What good looks like

Findings that name the *implementation* flaw precisely: "passwords stored as unsalted MD5," "AES-ECB reveals structure," "TLS 1.0 + RC4 enabled," "hardcoded AES key in the binary" — each with the correct modern fix.

## Detection & defence

| Weakness | Defence |
|---|---|
| Fast/unsalted password hashes | Argon2/bcrypt/scrypt with per-user salt |
| ECB / no-integrity modes | Authenticated encryption (AES-GCM, ChaCha20-Poly1305) |
| Weak/downgraded TLS | TLS 1.2+ only, strong ciphers, HSTS |
| Hardcoded/reused keys | Key management (KMS/HSM); rotate; never in source |
| Weak randomness | CSPRNGs for keys/tokens/IVs |

## Common junior mistakes

- Trying to "break AES" instead of finding the weak mode, key handling, or config around it.
- Calling base64/hex "encryption."
- Reporting a weak cipher without checking it's actually negotiable by a client.

## Go deeper

- OWASP — [Cryptographic Storage](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html) & [TLS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html) cheat sheets · [CyberChef](https://gchq.github.io/CyberChef/) · [testssl.sh](https://testssl.sh)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
