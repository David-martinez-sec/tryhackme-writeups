# TryHackMe — Public Key Cryptography: Notes & Quick-Reference

*First-person notes from completing the TryHackMe "Public Key Cryptography Basics" room. I practiced concepts around asymmetric cryptography, key exchange, RSA, Diffie‑Hellman, SSH keypairs, digital signatures, certificates, and OpenPGP.*

---

## Overview
I completed the Public Key Cryptography basics room and practiced the foundations of asymmetric cryptography. This doc captures my first-person notes, useful commands, and a short checklist I can reuse when working with keys, signatures, and certificates.

---

## What I learned (high-level)
- **Asymmetric cryptography** uses a key pair: a **private key** (kept secret) and a **public key** (shared). What one encrypts, only the other can decrypt (depending on the operation).
- **RSA** is a public-key algorithm based on factoring large integers; used for encryption and signatures.
- **Diffie‑Hellman (DH)** allows two parties to derive a shared secret over an insecure channel — foundational for secure key exchange (including ECDH variants).
- **SSH key pairs** (e.g., RSA, Ed25519) provide passwordless, stronger authentication for remote logins.
- **Digital signatures** provide integrity and non‑repudiation: sign with a private key, verify with the public key.
- **Certificates and PKI**: X.509 certificates bind identity to a public key; CAs issue certificates and build trust chains.
- **OpenPGP / GPG** offers decentralized key management for encryption and signing of messages/files.

---

## Core concepts & commands I practiced

### RSA (openssh / openssl)
- Generate a 2048-bit RSA private key with OpenSSL:
```
openssl genpkey -algorithm RSA -out rsa_private.pem -pkeyopt rsa_keygen_bits:2048
```
- Extract the public key:
```
openssl rsa -pubout -in rsa_private.pem -out rsa_public.pem
```
- Example: sign a file and verify (using OpenSSL):
```
openssl dgst -sha256 -sign rsa_private.pem -out file.sig file.txt
openssl dgst -sha256 -verify rsa_public.pem -signature file.sig file.txt
```

### SSH keys (ssh-keygen)
- Generate an Ed25519 keypair (recommended for modern SSH):
```
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -C "me@example.com"
```
- Generate RSA keypair (legacy / compatibility):
```
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -C "me@example.com"
```
- Copy public key to remote host for passwordless login:
```
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@host
```
- Use `ssh-agent` and `ssh-add` to load private keys into the agent:
```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### Diffie‑Hellman & ECDH (conceptual / OpenSSL)
- Classic DH and elliptic-curve ECDH produce a **shared secret** without sending the secret itself.
- Example: OpenSSL can generate parameters and keys for testing (conceptual):
```
openssl dhparam -out dhparams.pem 2048
# For ECC:
openssl ecparam -name prime256v1 -genkey -noout -out ecdh_key.pem
```
- In practice, TLS uses ephemeral ECDH (ECDHE) for forward secrecy.

### Certificates & PKI (OpenSSL)
- Create a private key and CSR (Certificate Signing Request):
```
openssl genpkey -algorithm RSA -out mykey.pem -pkeyopt rsa_keygen_bits:2048
openssl req -new -key mykey.pem -out mycsr.csr -subj "/C=US/ST=State/L=City/O=Org/CN=example.com"
```
- Self-sign a certificate (for testing only):
```
openssl x509 -req -in mycsr.csr -signkey mykey.pem -out mycert.pem -days 365
```
- Inspect a certificate:
```
openssl x509 -in mycert.pem -text -noout
```

### OpenPGP / GPG
- Generate a new GPG key:
```
gpg --full-generate-key
```
- Export public key:
```
gpg --output mypubkey.asc --armor --export me@example.com
```
- Encrypt and/or sign a file:
```
gpg --encrypt --recipient bob@example.com file.txt
gpg --sign --armor file.txt   # creates file.txt.asc
gpg --verify file.txt.asc
```

---

## Practical notes & gotchas I remind myself of
- **Key protection**: private keys must be stored securely (file permissions, hardware tokens like YubiKey, or HSMs).
- **Key sizes & algorithms**: prefer modern curves (Ed25519, P-256 for ECDSA/ECDH) or 2048+ bit RSA (4096 recommended for long-term, but consider performance).
- **Forward secrecy**: ephemeral DH (ECDHE) is important — avoids long-term key compromise leading to decryption of past sessions.
- **Certificates vs. raw keys**: certificates carry identity info and signatures from CAs — useful for trust chains; raw public keys (like GPG keys) are often used in webs of trust.
- **Checks and fingerprints**: always verify key fingerprints out-of-band (phone call, known directory) before trusting an unknown public key.
- **Revocation**: plan for key compromise (revoke certificates, publish GPG revocation certificates).
- **Compatibility**: some systems still require RSA; Ed25519 may be unsupported on older devices.

---

## Quick troubleshooting & inspection
- View public key fingerprint (SSH):
```
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```
- Show certificate fingerprint and details:
```
openssl x509 -in cert.pem -noout -fingerprint -text
```
- Check which algorithms server accepts (TLS):
```
openssl s_client -connect example.com:443
```
- Verify a detached signature:
```
openssl dgst -sha256 -verify pub.pem -signature file.sig file.txt
```

---

## Investigation & validation checklist
1. Confirm the **public key fingerprint** with the owner (out-of-band).
2. Verify **certificate chain** — check issuer, validity dates, and revocation status.
3. If analyzing a capture, look for **key exchange** handshakes (e.g., TLS ClientHello/ServerHello) and confirm ECDHE parameters for forward secrecy.
4. For GPG-signed artifacts, verify signatures and check signer's UID and key trust.
5. When migrating keys, ensure **permissions** are strict (`chmod 600` on private keys) and agent or token usage is configured.
6. If a key is suspected compromised, revoke it and re-issue new keys and certificates.

---

## My takeaway & next steps
- Public-key cryptography underpins secure communication: I now better understand RSA, Diffie‑Hellman, SSH keys, certificates, and OpenPGP.
- Next steps I plan: practice generating and verifying certificates, use GPG to sign/encrypt files and emails, and experiment with ECDHE in TLS handshakes (inspect with Wireshark).
- Also: practice key lifecycle tasks — rotate keys, create revocations, and document secure storage/backup processes.

---

## References / Further reading (short)
- OpenSSL and ssh-keygen man pages (local `man`).
- GPG / OpenPGP documentation.
- TryHackMe rooms and sample lab machines — practice is where it clicks.

---

*End of notes — saved as a Markdown file for my TryHackMe notes and GitHub learning log.*