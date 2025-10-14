# TryHackMe — Wireshark: The Basics — Notes & Cheat Sheet

*First-person notes and a quick-reference guide based on completing the "Wireshark: The Basics" room.*

---

## Overview
I completed the TryHackMe **Wireshark: The Basics** room and practiced using Wireshark to inspect network traffic, apply filters, find important packets and transferred files, extract packet data and bytes, and search for evidence relevant to investigations. This document captures what I learned in first person, plus a practical cheat sheet and an investigation checklist I can reuse.

---

## What I learned (high-level)
- Wireshark is a packet analyzer that captures and decodes network traffic into protocol-aware fields I can inspect.
- I used **display filters** to narrow what I see (these operate on decoded fields).
- I used **capture filters** when capturing live traffic to limit what gets recorded (BPF syntax).
- I located files transferred over protocols (HTTP, SMB, TFTP, FTP) using **Follow TCP Stream** and *Export Objects*.
- I inspected **packet bytes** (the raw payload/hex) and exported those bytes to disk when needed.
- I searched packets for strings, headers, and specific field values to support investigative goals (e.g., indicators of compromise, credentials, exfiltration).
- I practiced with both the GUI and command-line (`tshark`) for automation and scripted analysis.

---

## Quick GUI workflow (my steps when investigating a pcap)
1. **Open the capture** (`File → Open`) and let Wireshark index packets.
2. **Get the lay of the land**:
   - Look at the main columns: No., Time, Source, Destination, Protocol, Length, Info.
   - Use `Statistics → Protocol Hierarchy` and `Statistics → Conversations` to find noisy protocols and top talkers.
3. **Apply display filters** to zoom in on suspicious traffic (examples below).
4. **Follow streams**:
   - Right-click a suspected packet → `Follow` → `TCP Stream` (or UDP/HTTP as relevant) to reconstruct session payloads.
5. **Export objects** (useful for downloaded or exfiltrated files):
   - `File → Export Objects → HTTP` (or `SMB`, `TFTP`) and save files.
6. **Inspect raw bytes**:
   - Select a packet → `Packet Bytes` pane (shows hex + ASCII). Use `View → Show Packet Bytes` if hidden.
   - Right-click packet → `Export Packet Bytes...` to save selected packet payload to a file if needed.
7. **Save filtered capture**:
   - `File → Export Specified Packets` and choose `Displayed` to write a smaller pcap for evidence sharing.
8. **Automate with tshark** when repeating steps or extracting many fields.

---

## Helpful display filters (my personal cheat sheet)
> Notes: Wireshark display filters are case-sensitive and use protocol field names.

- IP and ports:
  - `ip.addr == 192.168.1.10` — any packet where the IP appears (src or dst).
  - `ip.src == 192.168.1.10` — packets from a source IP.
  - `ip.dst == 8.8.8.8` — packets to a destination IP.
  - `tcp.port == 80` — packets where either TCP source or destination port is 80.
  - `udp.port == 53` — DNS traffic (if using UDP port 53).

- Protocol-specific:
  - `http` — show only HTTP traffic.
  - `dns` — show DNS queries/responses.
  - `tls` or `ssl` — SSL/TLS traffic (handshakes, etc.).
  - `ftp || smb || tftp` — show FTP, SMB, or TFTP traffic.

- Content matching:
  - `frame contains "password"` — frames whose raw bytes contain the ASCII string `password`.
  - `http.request.uri contains "login"` — HTTP requests with `login` in the URI.
  - `dns.qry.name == "example.com"` — DNS queries for `example.com`.

- Suspicious indicators:
  - `tcp.flags.syn == 1 && tcp.flags.ack == 0` — SYN scans or connection attempts.
  - `tcp.analysis.retransmission` — retransmissions (could indicate instability or backdoors repeating).
  - `tcp.port == 4444` — examples of unusual ports often used by tools (adjust as relevant).

---

## Common investigative actions & how I do them

### Reconstructing sessions
- **Follow TCP Stream**: Right-click a TCP packet → `Follow` → `TCP Stream`. This shows the full conversation; I can change the displayed encoding and save the content as raw or text.
- **Reassemble HTTP**: Use `Follow TCP Stream` on HTTP requests/responses or use `File → Export Objects → HTTP` to list and save files the server sent.

### Extracting files
- `File → Export Objects → HTTP/SMB/TFTP` lists transfer sessions and allows me to save files (images, binaries, text) directly.
- If a transfer is in a TCP stream but not listed, I use `Follow TCP Stream` and save the payload manually.

### Viewing and saving raw bytes
- `Packet Bytes` pane shows hex + ASCII; I can select a range, right-click → `Export Selected Packet Bytes` to save to disk.
- For many packets, I can export the packet bytes for all displayed packets via `File → Export Packet Dissections` or write a script using `tshark` to extract fields/raw payload.

### Using tshark for field extraction (examples I use)
- List HTTP hosts and URIs:
  ```
  tshark -r capture.pcap -Y http.request -T fields -e http.host -e http.request.uri
  ```
- Extract DNS query names:
  ```
  tshark -r capture.pcap -Y dns.qry.name -T fields -e dns.qry.name
  ```
- Extract raw frame bytes (useful for scripting):
  ```
  tshark -r capture.pcap -T fields -e frame.number -e frame.len -e frame.time
  ```
  (Note: extracting raw binary payloads for many packets often needs tailored field choices like `data.data` or external parsing; I test fields interactively.)

---

## Investigation checklist (my go-to steps)
1. Open pcap and run protocol summary (`Statistics → Protocol Hierarchy`).
2. Identify top talkers (`Statistics → Conversations`).
3. Filter on interesting IPs/ports and protocols.
4. Search for keywords (`frame contains "password"`) and suspect file transfers (`http`, `smb`, `tftp`).
5. Reconstruct sessions with `Follow TCP Stream`.
6. Export any suspicious files (`Export Objects`) and compute hashes (outside Wireshark — `sha256sum`).
7. Save a filtered pcap containing only relevant traffic for sharing.
8. Document steps: filters used, packets of interest (frame numbers), exported filenames and hashes, and why they matter.

---

## My personal takeaways & tips
- **Start broad, then narrow** — use protocol hierarchy to guide where to focus.
- **Display filters are your best friend** — learn the common field names you care about (http.*, dns.*, ip.*, tcp.*, tls.*, smb.*).
- **Follow streams before jumping to conclusions** — reconstructing the conversation often reveals context.
- **Export objects** — it saves time and prevents manual reconstruction errors.
- **Use tshark for repeatable extraction** — once I know the fields I want, I automate with `tshark` to generate CSVs or lists.
- **Document everything** — investigative work must be reproducible (filters, timestamps, frame numbers).

---

## Next steps I plan to practice
- More labs focusing on protocol-specific extraction (HTTP downloads, DNS exfiltration).
- Using `tshark` to automate detection of suspicious patterns (e.g., DNS over HTTP or data in TXT fields).
- Combining Wireshark with `strings`, `binwalk`, and file-hash checks to analyze exported binaries.

---

## References / Further reading (short list)
- Wireshark User Guide (local help in Wireshark and the built-in display filter reference).
- TryHackMe tasks and sample pcaps — practice frequently to build pattern recognition.

---

## Appendix: Example filters I use regularly
- `ip.addr == 10.10.14.2 && tcp.port == 80`
- `http.request && http.request.method == "POST"`
- `frame contains "Authorization:"`
- `dns.flags.response == 1 && dns.a`  (DNS responses with A records)
- `tcp.analysis.lost_segment || tcp.analysis.retransmission`

---

*End of notes — generated for my TryHackMe Wireshark practice. Saved as a Markdown file for quick reference and to include in my GitHub learning notes.*