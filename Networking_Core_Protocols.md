# Networking Core Protocols — TryHackMe Room: Detailed Notes

**Author:** David Martinez  
**Source:** TryHackMe — *Networking Core Protocols* (room completed)  
**Date:** 2025-10-07

---

## Overview

This document is a verbose, GitHub-ready summary of the TryHackMe **Networking Core Protocols** room. The room explored low-level and application-layer protocols that form the backbone of modern networked communication. While the room briefly referenced Telnet historically, the primary protocols covered are:

- DNS (Domain Name System)  
- HTTP (HyperText Transfer Protocol)  
- FTP (File Transfer Protocol)  
- SMTP (Simple Mail Transfer Protocol)  
- POP3 (Post Office Protocol v3)  
- IMAP (Internet Message Access Protocol)

The exercises used **Wireshark** to inspect client–server interactions and emphasize how protocols behave behind GUIs and web clients. The goal of this document is to expand that material, explain how each protocol works, list common ports and commands, provide example Wireshark observations, and suggest follow-up learning steps.

---

## Learning Objectives

By the end of this room and this summary you should be able to:

1. Explain the purpose and basic operation of DNS, HTTP, FTP, SMTP, POP3, and IMAP.  
2. Identify default ports and protocol layers for each service.  
3. Interpret simple Wireshark captures showing request/response sequences.  
4. Recognize basic security weaknesses in plaintext protocols and understand modern secure alternatives.  
5. Use common client commands to interact with services for testing and debugging.

---

## Quick Reference: Ports & Protocol Types

- **DNS** — UDP/TCP port **53** — Application layer (name resolution)  
- **HTTP** — TCP port **80** — Application layer (web traffic)  
- **HTTPS** — TCP port **443** — Application layer (encrypted web traffic using TLS)  
- **FTP** — TCP ports **20 (data)** and **21 (control)** — Application layer (file transfer)  
- **SMTP** — TCP port **25** (submission variants: 587, 465 for SMTPS) — Application layer (mail transfer)  
- **POP3** — TCP port **110** (POP3S: 995) — Application layer (mail retrieval)  
- **IMAP** — TCP port **143** (IMAPS: 993) — Application layer (mail retrieval/synchronization)  
- **Telnet** — TCP port **23** — Application layer (remote terminal, plaintext; historical)

---

## 1) DNS — Domain Name System

### Purpose
DNS translates human-friendly domain names (e.g., `example.com`) into IP addresses (e.g., `93.184.216.34`) used by networking stacks. It is a distributed, hierarchical database with caching at multiple levels.

### Basic Flow
1. Client (resolver) asks a recursive DNS resolver (often provided by an ISP or public DNS like 8.8.8.8).  
2. If resolver lacks an answer, it queries root servers → TLD servers → authoritative servers.  
3. The resolver returns the final A/AAAA/CNAME/MX/TXT/etc. record to the client.

### Common Record Types
- **A** — IPv4 address  
- **AAAA** — IPv6 address  
- **CNAME** — canonical name (alias)  
- **MX** — mail exchange (mail server)  
- **TXT** — arbitrary text (SPF, DKIM, other metadata)  
- **NS** — authoritative name server

### Wireshark Observations
- DNS typically uses UDP port 53; large responses or zone transfers may use TCP.
- Look for **Standard query** and **Standard query response** lines.  
- You can filter with `dns` or `udp.port == 53` and inspect individual resource records in the packet details pane.

### Security Considerations
- DNS traffic is frequently unencrypted (plain UDP). Use **DNS-over-HTTPS (DoH)** or **DNS-over-TLS (DoT)** when privacy is needed.  
- Attack types: DNS spoofing, cache poisoning, DDoS against name servers.

---

## 2) HTTP — HyperText Transfer Protocol

### Purpose
HTTP is the protocol used to request and transfer web content (HTML, CSS, JS, images). It is a stateless request/response protocol built on TCP.

### Basic Flow (HTTP/1.1)
1. Client opens a TCP connection to server (port 80).  
2. Client sends an HTTP request (method, path, headers). Example: `GET /index.html HTTP/1.1`.  
3. Server responds with status line (e.g., `HTTP/1.1 200 OK`), headers, and optional body.

### Common HTTP Methods
- `GET` — retrieve resource  
- `POST` — submit data to server  
- `PUT` / `PATCH` — update resource  
- `DELETE` — delete resource  
- `HEAD` — same as GET but no body (headers only)

### Important Headers
- `Host` — required for virtual hosting  
- `User-Agent` — client identifier  
- `Content-Type` / `Content-Length` — body MIME and size  
- `Connection: keep-alive` — persistent connections

### Wireshark Observations
- Filter with `http` or `tcp.port == 80`.  
- You will see `GET` and `HTTP/1.1 200 OK` pairs.  
- Follow the TCP stream to view full request/response payloads.

### Security Considerations
- **HTTP is plaintext**. Credentials and sensitive data are exposed. Use **HTTPS (TLS)** on port 443.  
- Modern web security also involves HSTS, secure cookies, and TLS certificate validation.

---

## 3) FTP — File Transfer Protocol

### Purpose
FTP transfers files between clients and servers. FTP supports two channels: a control channel (commands) and data channel (file transfer).

### Modes
- **Active FTP**: Server initiates data connection to client (server port 20 → client ephemeral port).  
- **Passive FTP (PASV)**: Client opens both control and data connections to the server (used commonly with NAT/firewalls).

### Commands & Responses (control channel)
- `USER` — provide username  
- `PASS` — provide password  
- `LIST` — list directory  
- `RETR` — retrieve file  
- `STOR` — upload file  
Responses are numeric codes like `220` (service ready), `331` (user okay, need password), `226` (closing data connection).

### Wireshark Observations
- Control channel uses TCP port 21. Data channel often uses ephemeral ports negotiated during session.  
- Filter with `ftp` or `tcp.port == 21`. View control commands/responses in plaintext.

### Security Considerations
- FTP transmits credentials and data in plaintext. Use **FTPS** (FTP over TLS) or **SFTP** (SSH File Transfer Protocol — different protocol over SSH) for secure transfers.

---

## 4) SMTP — Simple Mail Transfer Protocol

### Purpose
SMTP is used to transfer email between mail servers and from email clients (submission) to servers.

### Ports and Variants
- Port **25** — SMTP server-to-server transfer (legacy default).  
- Port **587** — Message submission (authenticated, recommended).  
- Port **465** — SMTPS (SMTP over TLS) historically used for implicit TLS.

### Basic Flow
1. Client/server establish TCP connection.  
2. SMTP handshake: `EHLO`/`HELO`.  
3. Mail transaction commands: `MAIL FROM:`, `RCPT TO:`, `DATA` (message content), `.` (end of data).  
4. Server replies with 2xx/3xx/4xx/5xx codes.

### Wireshark Observations
- Filter with `smtp` or `tcp.port == 25`.  
- Inspect commands like `EHLO`, `MAIL FROM`, `RCPT TO`, and `DATA`. The message headers and body are visible in plaintext unless STARTTLS is negotiated.

### Security Considerations
- Without TLS / STARTTLS, email headers and bodies (and sometimes credentials) are plaintext in transit. Use **STARTTLS** or SMTPS.  
- Email authentication mechanisms: SPF, DKIM, and DMARC help protect against spoofing.

---

## 5) POP3 — Post Office Protocol v3

### Purpose
POP3 is a simple protocol for retrieving email from a mail server. Its main model is "download-and-delete" (by default) — messages are pulled to the client and often removed from the server.

### Port & Flow
- Port **110** — POP3 plaintext  
- Port **995** — POP3S (POP3 over TLS)
- Commands: `USER`, `PASS`, `STAT`, `LIST`, `RETR`, `DELE`, `QUIT`.

### Wireshark Observations
- Filter with `pop` or `tcp.port == 110`.  
- You will see `USER` and `PASS` in plaintext on unencrypted sessions.

### Security Considerations
- POP3 is simple but limited for multi-device synchronization. Use IMAP or secure POP3 (TLS) if you need encryption.

---

## 6) IMAP — Internet Message Access Protocol

### Purpose
IMAP is a more full-featured mail retrieval and synchronization protocol than POP3. It allows clients to manage and synchronize mailboxes on the server (folders, flags, partial fetches).

### Port & Flow
- Port **143** — IMAP plaintext  
- Port **993** — IMAPS (IMAP over TLS)
- Common commands: `LOGIN`, `SELECT`, `FETCH`, `SEARCH`, `STORE`, `UID`, `LOGOUT`.

### Wireshark Observations
- Filter with `imap` or `tcp.port == 143`.  
- IMAP commands and responses are visible; with TLS enabled, the session will be encrypted and less readable.

### Security Considerations
- Use IMAPS (TLS) for encryption. IMAP is well-suited for multi-device access and rich client features, but must be secured to prevent credential exposure.

---

## Telnet — Historical note

Telnet (TCP port 23) was discussed in the room as an example of an early plaintext remote terminal protocol. Telnet transmits everything — including credentials — unencrypted. For remote command shell access, **SSH** (Secure Shell) is the modern, secure replacement.

---

## Wireshark: Practical Tips & What To Look For

- **Filters**: Use protocol names as filters (e.g., `dns`, `http`, `ftp`, `smtp`, `imap`, `pop`). You can also filter by port (e.g., `tcp.port == 110`) or IP addresses (`ip.addr == 192.168.1.10`).  
- **Follow TCP Stream**: Right-click a packet → “Follow” → “TCP Stream” to view the full request/response conversation as text. This is essential for reading HTTP requests or SMTP sessions.  
- **Inspect Flags**: For TCP, check SYN/ACK flags to understand the handshake and session establishment.  
- **Reassemble**: Wireshark can reassemble segmented TCP payloads so you can read full HTTP bodies or large DNS responses.  
- **Statistical Tools**: Use `Statistics → Conversations` or `Statistics → Protocol Hierarchy` to get a high-level view of traffic distribution.

---

## Common Attack / Security Observations

- **Plaintext Protocol Exposure** — Telnet, FTP, POP3, IMAP (without TLS), HTTP (without HTTPS): credentials can be captured via network sniffing.  
- **DNS Attacks** — Spoofing/cache poisoning, DNS rebinding, DNS amplification DDoS.  
- **Email Threats** — Phishing, spoofed headers, and interception when TLS is not used.  
- **Mitigations** — Use TLS (HTTPS, IMAPS, POP3S, STARTTLS for SMTP), enforce strong authentication, monitor DNS integrity, and use SPF/DKIM/DMARC for email.

---

## Example Wireshark Captures — What You Would See (Short Walkthroughs)

### DNS Query Example
- Filter: `dns`  
- Observation: A packet from client UDP:53 query for `example.com`. The response contains an **A** record with the IPv4 address.

### HTTP GET Example
- Filter: `http` or `tcp.port == 80`  
- Observation: `GET /index.html HTTP/1.1` followed by `Host:` header. Server sends `HTTP/1.1 200 OK` and the response body with HTML.

### SMTP Session Example
- Filter: `tcp.port == 25`  
- Observation: `220 mail.example.com ESMTP` → `EHLO client.example.com` → authentication (if present) and `MAIL FROM:` / `RCPT TO:` / `DATA`. Without STARTTLS, full message content is readable.

---

## Practical Commands & Tools

- **dig** — Query DNS servers:
  - `dig example.com A`
  - `dig @8.8.8.8 example.com`
- **curl** — Fetch HTTP resources:
  - `curl -v http://example.com/`
  - `curl -I https://example.com/` (show headers)
- **ftp** — Command-line FTP client:
  - `ftp ftp.example.com` → `USER` / `PASS` → `ls` / `get` / `put`
- **openssl s_client** — Test TLS-enabled services:
  - `openssl s_client -connect example.com:443` (HTTPS)
  - `openssl s_client -starttls smtp -connect mail.example.com:587` (SMTP STARTTLS)
- **telnet** (for testing plaintext TCP services; not secure for real use):
  - `telnet mail.example.com 25` to interact with SMTP (text prototyping)

---

## Reflection & Best Practices

- Understanding these core protocols is essential for both defensive and offensive security tasks. Packets on the wire show the raw truth: GUIs and APIs abstract complexity, but Wireshark reveals the underlying conversations.  
- Always assume unencrypted, legacy protocols are visible to a network adversary. Use secure variants (TLS, SSH, SFTP).  
- Practice capturing and analyzing traffic in a controlled lab. Simulate clients and servers, and observe how handshakes, errors, retransmissions, and protocol options appear in a packet capture.

---

## Suggested Next Steps / Further Reading

- Try hands-on labs:
  - Capture a local browser session to a test HTTP server and inspect the `GET`/`POST` flows.  
  - Set up an SMTP server (in a lab environment) and observe the `EHLO`/`MAIL FROM` procedure in Wireshark.  
- Read RFCs for primary protocols (RFC 1034/1035 for DNS, RFC 7230–7235 for HTTP/1.1, RFC 959 for FTP, RFC 5321 for SMTP, RFC 1939 for POP3, RFC 3501 for IMAP).  
- Learn TLS fundamentals and practice with tools like `openssl` and `sslyze`.  
- Explore DNS security: DNSSEC, DoT, and DoH.

---

## Appendix: Short Command Examples

```bash
# DNS lookup
dig +short example.com

# HTTP GET (verbose)
curl -v http://example.com/

# HTTPS handshake (view cert)
openssl s_client -connect example.com:443

# SMTP test (use STARTTLS where available)
telnet mail.example.com 25
EHLO local.example.com
MAIL FROM:<you@example.com>
RCPT TO:<target@example.com>
DATA
Subject: Test
This is a test.
.
QUIT

# FTP (control channel)
ftp ftp.example.com
# then: USER, PASS, LIST, RETR, STOR
```

---

## A Short Note About This Document

This markdown file expands on the TryHackMe room summary you provided. It’s written to be verbose, educational, and useful as a reference for study notes, GitHub README content, or a printable summary for offline review.

---

**Completed by:** David Martinez (notes adapted and expanded from TryHackMe room).  
**Date:** 2025-10-07
