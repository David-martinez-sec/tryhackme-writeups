
# TryHackMe — Networking: Secure Protocols (Room Notes & Flag Hunt)

**Author:** David Martinez  
**Context:** Google Cybersecurity Certificate / TryHackMe — beginner → intermediate level  
**Purpose:** GitHub-ready notes summarizing protocol concepts (HTTP vs HTTPS, SSL/TLS) and a practical Wireshark lab where a decrypted pcap was inspected to extract a flag (password) from an HTTP/2 POST.

---

## Overview
This short write-up captures the key concepts and hands-on steps from the room:

- Core concepts: plain vs secure protocols (e.g., HTTP → HTTPS), what SSL/TLS do, and why ephemeral key exchange matters.  
- Lab work: using Wireshark to inspect a **decrypted** TLS capture (HTTP/2), finding a POST to `/ListAccounts...`, reassembling frames/streams, and extracting a password from the URL-encoded payload.  
- Practical takeaways and commands/filters to reuse on future labs.

---

## Learning objectives
- Understand the difference between application protocols and their secure counterparts (HTTP vs HTTPS, FTP vs FTPS/FTPS-implicit/explicit, SMTP vs SMTPS/STARTTLS).  
- Know what a certificate is, where it is presented, and how the TLS handshake authenticates a server.  
- Be able to find plaintext credentials in captures using Wireshark when TLS is decrypted (HTTP/2 HEADERS/DATA or HTTP/1.x POST bodies).  
- Use basic Wireshark features: **Follow TCP Stream**, **Packet Bytes search**, display filters, and reassembly preferences.

---

## Protocols & secure counterparts (cheat-sheet)
- **HTTP (80)** → **HTTPS (443)** (TLS/SSL)  
- **FTP (21)** → **FTPS (990 implicit)** or **FTP + STARTTLS (explicit)**  
- **SMTP (25)** → **SMTPS (465 implicit)** or **SMTP + STARTTLS**  
- **IMAP (143)** → **IMAPS (993)**  
- **POP3 (110)** → **POP3S (995)**  
- **LDAP (389)** → **LDAPS (636)**  
- **SSH (22)**: already encrypted (not TLS)

> Note: secure ports are conventions. TLS can run on nonstandard ports; always confirm by seeing a TLS **ClientHello**/handshake.

---

## SSL vs TLS — plain summary
- **SSL** is the older protocol family; **TLS** is the modern replacement.  
- **TLS** provides: confidentiality (encryption), integrity (MAC/AEAD), and authentication (X.509 certs).  
- **TLS 1.2 vs TLS 1.3** differences (high level): TLS 1.3 simplifies the handshake, uses ephemeral key exchange by default for forward secrecy, and encrypts more handshake messages earlier.

---

## Certificates — where & how
- Certificates (X.509) are server IDs containing the public key, CN/SAN, issuer (CA), and validity dates.  
- They are **sent by the server** during the TLS handshake (certificate chain).  
- Clients verify the chain up to a trusted root (trust store), validate CN/SAN, check dates, and (best-effort) revocation (OCSP/CRL).  
- A valid certificate + verified signature ties ephemeral key exchange values to the server’s identity (prevents basic MITM).

---

## Why ephemeral key exchange matters (TL;DR)
- **Ephemeral (E)DH**: both sides generate short-lived keypairs, exchange public halves, and each side computes the same shared secret locally.  
- The symmetric session key is **never sent**; it is derived.  
- **Forward secrecy**: if long-term keys leak later, past sessions remain safe because ephemeral private keys were not stored.

---

## Lab: Wireshark flag hunt (decrypted TLS / HTTP/2)
**Goal:** find the password (flag) in a decrypted pcap showing HTTP/2 traffic.

### Key observations
- The capture had TLS decrypted (Wireshark showed HTTP/2 HEADERS and DATA).  
- The relevant flow: `HEADERS: POST /ListAccounts?...` (packet X) followed by `DATA (application/x-www-form-urlencoded)` (packet Y).  
- The password began with the known prefix `thm` (useful clue).

---

## Practical Wireshark workflow (step-by-step)
1. **Confirm TLS is decrypted**  
   - Check that Wireshark dissects TLS payload into HTTP/2 (HEADERS/DATA). If you only see `Application Data` / encrypted bytes, TLS is not decrypted.

2. **Locate likely POST(s)**  
   - Display filter:  
     ```
     http2 && frame contains ":method: POST"
     ```
   - Or visually scan packet list for `HEADERS: POST /...` lines.

3. **Open the relevant packet and expand**  
   - Middle pane → `Hypertext Transfer Protocol 2` → `Headers` (inspect pseudo-headers like `:method`, `:path`, and ordinary headers such as `Authorization` or `Cookie`).

4. **Inspect posted body (DATA frames)**  
   - Click the next `DATA` frame (often labeled `application/x-www-form-urlencoded`) and expand `Data` to see the content if Wireshark reassembled it.

5. **If DATA/headers are fragmented or not visible**  
   - Right-click any packet from the flow → **Follow → TCP Stream**.  
   - In the stream window: set **Show data as:** ASCII/Raw and use Ctrl+F to search for `password`, `username`, or your known prefix (e.g., `thm`).

6. **If Follow TCP Stream shows binary or nothing**  
   - Enable reassembly: `Edit → Preferences → Protocols → TCP` → check `Allow subdissector to reassemble TCP streams` and `Reassemble out-of-order segments`.  
   - For HTTP/2 reassembly: `Edit → Preferences → Protocols → HTTP2` → enable header & DATA reassembly options. Reload the capture or re-open the stream.

7. **If display filters fail, search raw packet bytes**  
   - `Edit → Find Packet` → **Search In:** Packet bytes → search for ASCII `thm` or hex `74 68 6d`. This is helpful when strings are split across frames/packets.

8. **Decode extracted content**  
   - URL-decode `%xx` sequences, base64-decode Base64 blobs, hex-decode if needed.

---

## Handy Wireshark filters & commands
### Wireshark display filters
```
http2
http2 && frame contains ":method: POST"
http2 && frame contains "password"
http2 && frame contains "Authorization:"
frame contains "thm"       # raw frame search (may fail if string split)
```

### One-liners for decoding
URL-decode:
```bash
python3 -c "import urllib.parse,sys; print(urllib.parse.unquote(sys.argv[1]))" 'username=joe%40mail.com&password=thm%21abc'
```
Base64 decode:
```bash
echo 'QWxhZGRpbjp0aG1wYXNzd29yZA==' | base64 --decode
```
Hex → ASCII:
```bash
echo '74686d70617373' | xxd -r -p
```

### tshark examples
```bash
# List frames containing literal bytes 'thm'
tshark -r capture.pcapng -Y 'frame contains "thm"' -T fields -e frame.number -e ip.src -e ip.dst -e tcp.stream

# Dump reassembled raw TCP stream N
tshark -r capture.pcapng -q -z follow,tcp,raw,<STREAM-N> > stream-<N>.raw
```

---

## What I did well (reflection)
- Focused on the likely POST flow (`/ListAccounts`), then moved to the associated DATA frame — correct forensic intuition.  
- Used **Follow TCP Stream** and **Packet Bytes find** when display filters didn’t return results.  
- Extracted and decoded the password from a URL-encoded string — robust practical skill for many labs.

---

## Suggestions / quick tips for next time
- Enable TCP & HTTP/2 reassembly early to avoid fragmentation issues.  
- Use `Follow TCP Stream` early when looking for reassembled content.  
- If you know a string prefix (like `thm`), try ASCII, Base64 (`dGht`), and hex (`74686d`) searches.  
- Keep small decoding one-liners ready in your notes.

---

## Example Findings (redacted)
> During the lab, the POST to `/ListAccounts?...` contained an `application/x-www-form-urlencoded` payload. The password was contained in the URL-encoded body and began with `thm`. After URL-decoding the payload, the password was extracted and used to complete the flag task.

*(I’m intentionally not including the flag here — keep room write-ups non-spoiler for public repos if desired.)*

---

## Next steps / study plan
- Continue practicing: capture simple HTTP POSTs locally and find credentials in Wireshark.  
- Practice HTTP/2 captures: set up a small local server that serves HTTP/2 and POST JSON login payloads.  
- Learn to identify TLS handshakes in packet captures and confirm forward secrecy (ECDHE) usage.  
- Optional: learn basic tshark scripting to automate raw-string hunts on large pcaps.

---

## TL;DR (one-liner for README)
> TLS encrypts traffic; if you can decrypt a TLS capture, look for credentials in HTTP/2 HEADERS or DATA frames (POST bodies or `Authorization:` headers). Use **Follow TCP Stream**, enable reassembly, and decode url/base64/hex as needed.

---
