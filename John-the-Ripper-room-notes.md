# TryHackMe — John the Ripper: Room Notes & Walkthrough

**Author:** David Martinez  
**Date:** 2025-10-23  
**Room:** John the Ripper — The Basics (Task work + deep dive)  
**Purpose:** Share a concise, practical write-up of what I learned and the commands I used while completing the room. Suitable for a GitHub repo as a study note / walkthrough.

---

## TL;DR
Completed the John the Ripper room. Cracked a variety of hashes (passwords, `/etc/shadow` SHA512crypt entries, authentication hashes, ZIP file password, and an SSH RSA passphrase). Explored custom rules / mangling, masks, incremental attacks, and common troubleshooting when wordlists or network access are limited. Learned practical file-transfer techniques between AttackBox and target VMs and how `john --show` outputs change by input format.

---

## Environment
- VM image used: Ubuntu 24 (room target) and TryHackMe AttackBox (attacker).
- Tools used: `john` (John the Ripper), `unshadow`, `hash-identifier`/`hashid` (when available), `scp`, `python3 -m http.server`, `nc`, `zcat`, `gzip`.
- Wordlists: `rockyou.txt` (gzipped on Kali; missing on Ubuntu image — downloaded/uploaded or used subset).

---

## What I cracked (examples)
- Plaintext passwords (single-line hashes).
- System account SHA512crypt entries from `/etc/shadow` (cracked after `unshadow` and using `--format=sha512crypt`).
- Authentication hashes (various formats detected via `hashid` / John auto-detect).
- Password-protected ZIP file (used `john --format=zip` or `fcrackzip` depending on approach).
- SSH RSA private key passphrase (used `ssh2john` to convert and then cracked with John).

> Note: filenames and exact hashes intentionally omitted — include them in private notes only if you own the VM/data.

---

## Key commands & workflow

### 1. Inspect / prepare hashes
```bash
# view the first lines
cat -n unshadowed.txt | sed -n '1,10p'

# combine passwd + shadow if needed (on attacker)
unshadow /etc/passwd /etc/shadow > unshadowed.txt
```

### 2. Run John with a wordlist
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt
john --show unshadowed.txt    # view cracked entries (reconstructed passwd style)
```

### 3. Use rules to expand a small wordlist
```bash
john --wordlist=~/rockyou_subset_200.txt --rules --format=sha512crypt unshadowed.txt
```

### 4. Mask attacks (targeted brute-force)
```bash
john --mask='?l?l?l?l?l?d?d' --format=sha512crypt unshadowed.txt
```

### 5. Incremental (full brute-force — slow)
```bash
john --incremental=All --format=sha512crypt unshadowed.txt
```

### 6. SSH private key passphrase (convert before cracking)
```bash
# convert a key to john format
ssh2john id_rsa > id_rsa.john
john --wordlist=rockyou.txt id_rsa.john
```

### 7. ZIP file example
```bash
# if zip2john available:
zip2john archive.zip > archive.john
john --wordlist=rockyou.txt archive.john
```

---

## Troubleshooting notes & tips

- **"No password hashes left to crack"** — usually means John loaded the hash and either (a) the hash is already cracked (check `john --show`) or (b) the input format was not a crackable entry. Check the file with `cat -n` and inspect `~/.john/john.pot` to confirm.
- **`john --show` format differences** — when you run John against an `unshadow` file, `john --show` prints reconstructed passwd lines like `user:password:UID:GID:...`. For raw hashfiles, `john --show` prints hash→password (pot-style). Use `awk -F: '{print $1 ":" $2}'` to extract `user:password`.
- **Missing rockyou or no internet in VM** — TryHackMe task VMs often block outbound access. Common workarounds used in the room:
  - Download rockyou on your host and upload to AttackBox (TryHackMe UI, `scp`, or `python -m http.server`).
  - Use process substitution or inline small wordlists:
    ```bash
    john --wordlist=<(printf "password
123456
qwerty
") unshadowed.txt
    ```
  - Use masks and rules when wordlists are not available. They’re faster for targeted patterns.
- **File transfer between AttackBox and target** — preferred flow: *pull* the hash files from target to AttackBox using `scp` if SSH is enabled; otherwise spin up a simple HTTP server on the side that can serve files (`python3 -m http.server 8000`) and `wget`/`curl` from the other side. If neither works, use `nc` or base64 paste-copy as a last resort.
- **`scp` requires a destination** — remember: `scp -r user@host:/remote/dir .` copies the remote dir into the current local directory. Without the destination argument, `scp` errors out.

---

## Lessons learned / reflections
- John’s power comes from combining **good wordlists** with **rules/mangling** and **targeted masks**. A small, well-chosen wordlist + rules often beats blind brute force.
- Understanding hash formats is critical — use `hashid`/`hash-identifier` and John’s `--format` to avoid format mismatches.
- Practical ops matter: being able to move files between AttackBox and target, knowing how to work around network restrictions, and using process substitution are valuable skills for CTFs and labs.
- Converting containerized or vendor-specific protected files (ZIP, SSH keys) to John-compatible formats (`zip2john`, `ssh2john`) is essential to include them in your cracking workflow.

---

## Next steps / ideas to explore
- Learn advanced John rule creation and test custom rule sets.
- Explore `Hashcat` for GPU-accelerated cracking and compare workflows.
- Build a curated wordlist based on target recon (usernames, company words, dates) and test targeted masks.
- Document a short script to automate common conversions: `unshadow`, `ssh2john`, `zip2john`, and a standardized john command template.

---

## References & useful links
- John the Ripper documentation — official README and usage.
- TryHackMe room resources and walkthroughs.
- RockYou wordlist (public mirror).

---

## Notes for GitHub
- Keep actual hashes and sensitive material out of public repos. Replace real data with sanitized examples if you publish this README.
- Consider adding a simple `how-to-run` script or a Gist with the minimal commands used in the room.

---

**End of notes.**
