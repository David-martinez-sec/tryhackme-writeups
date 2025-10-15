# TryHackMe — tcpdump: Notes & Quick-Reference

*First-person notes based on my exploration of tcpdump. I practiced basic packet capture, filters (basic & advanced), display options, reading pcap files, and searching large logs for specific packets using tcpflags. This was a base-level walkthrough but it reminded me how powerful this small tool is.*

---

## What I explored (overview)
I used `tcpdump` to capture packets, save captures to files, and read captures back. I learned to apply basic and advanced Berkeley Packet Filter (BPF) expressions to target hosts, ports, protocols, and even specific TCP flags. I practiced display options (ASCII and hex), and I combined `tcpdump` output with traditional UNIX tools (`grep`, `awk`, `sed`) to search through large logs for patterns or suspicious packets.

---

## Core commands I used (my go-to commands)

### Basic capture
- Capture 100 packets on interface `eth0`:
```
sudo tcpdump -i eth0 -c 100
```

- Capture all packets (until stopped) and write to a pcap file:
```
sudo tcpdump -i eth0 -w capture.pcap
```

### Reading a pcap file I previously saved
- Read `capture.pcap` with numeric addresses and verbose output:
```
tcpdump -r capture.pcap -nn -vvv
```

### Useful display/output options I rely on
- `-n` : do not resolve hostnames (faster, clearer IPs).
- `-nn`: also do not resolve port names.
- `-v`, `-vv`, `-vvv`: increase verbosity (more protocol details).
- `-s 0`: set snaplen to capture full packets (very important to get full payloads).
- `-A`: print packets in ASCII (good for text-based protocols like HTTP).
- `-X` or `-xx`: print packet contents in hex and ASCII.
- `-tttt`: human-readable timestamp format.
- Examples:
```
tcpdump -r capture.pcap -nn -tttt -A
tcpdump -i eth0 -s 0 -nn -X
```

---

## Basic BPF filters I used (examples)
_BPF expressions are evaluated by libpcap before the packet is captured/displayed — this makes tcpdump efficient._

- Host filters:
```
tcpdump host 192.168.1.10
tcpdump src host 10.0.0.5
tcpdump dst host 8.8.8.8
```

- Port filters:
```
tcpdump port 80
tcpdump tcp port 443
tcpdump src port 22 and dst port 1025
```

- Protocol filters:
```
tcpdump icmp
tcpdump udp
tcpdump udp and port 53   # DNS over UDP
```

- Logical combinations:
```
tcpdump 'tcp and (port 80 or port 443)'
tcpdump 'host 10.0.0.2 and not port 22'
```

---

## Advanced filters & tcpflags (how I searched for specific TCP flag patterns)
I practiced filtering packets by TCP flags to find SYN scans, completed handshakes, or unusual traffic.

- Filter SYN packets only (SYN set, ACK not set):
```
tcpdump 'tcp[13] & 0x02 != 0 and tcp[13] & 0x10 == 0'
```
Explanation: `tcp[13]` accesses the TCP flags byte; `0x02` is SYN, `0x10` is ACK.

- Filter packets where ACK is set:
```
tcpdump 'tcp[13] & 0x10 != 0'
```

- Filter RST packets:
```
tcpdump 'tcp[13] & 0x04 != 0'
```

- Capture SYN+ACK responses:
```
tcpdump 'tcp[13] & 0x12 == 0x12'
```
Explanation: `0x12` is SYN (0x02) + ACK (0x10).

These expressions let me quickly search for scans, half-open connections, or abnormal flag combinations.

---

## Searching large pcap/log files (my workflow)
When dealing with large captures, I used a combination of `tcpdump`'s read mode and text processing tools:

- Quick textual scan for a keyword (e.g., "password") across ASCII packet dumps:
```
tcpdump -r big_capture.pcap -nn -A -s 0 | grep -i password -n
```

- Narrow first, then inspect:
```
tcpdump -r big_capture.pcap -nn 'host 10.10.14.5 and tcp' -s 0 -A | less
```

- Find SYN packets and show packet summary (then use frame number or timestamps to extract more):
```
tcpdump -r big_capture.pcap -nn -tttt 'tcp[13] & 0x02 != 0' > syn_packets.txt
```
Then I can review `syn_packets.txt` or pipe it to `awk`/`grep` for finer searching.

- Combining tcpdump with `awk` to extract timestamps & IPs:
```
tcpdump -r big_capture.pcap -nn -tttt 'tcp[13] & 0x02 != 0' | awk '{print $1, $3, $5}'
```
(Fields vary by output format; I adjust the `awk` indexes after testing.)

---

## Extra tips I learned / reminders to myself
- Always use `-s 0` if I want full payloads — default snaplen may truncate.
- Use `-nn` during triage to avoid slow DNS/port lookups messing up output.
- When capturing live on a busy host, use capture filters (BPF) to reduce pcap size, e.g. `tcpdump -i eth0 -w out.pcap 'host 10.0.0.5 and port 80'`.
- For scripted extraction and reproducibility, `tcpdump -r` piped into `grep`/`awk`/`sed` is simple and effective — but for structured extraction `tshark` often offers field-based exports which can be easier to parse.
- Remember file permissions: raw capturing requires root / sudo; captured files may contain sensitive data and should be protected.
- If I need packet numbers (frame indices) for referencing, Wireshark/tshark are more convenient; `tcpdump` output does not always include a simple frame index.

---

## Investigation checklist (tcpdump-focused)
1. Decide if I should capture live or analyze an existing pcap.
2. If capturing live: choose interface `-i`, snaplen `-s 0`, and a capture filter to limit data.
3. Save raw pcap with `-w` for later forensic work.
4. Read the pcap with `-r` and `-nn -tttt -A/-X` to view payloads as needed.
5. Use BPF filters to zoom in on hosts, ports, or tcpflags (SYN/ACK/RST).
6. Pipe output to `grep`/`awk`/`sed` for searching large logs.
7. Export suspicious payloads (with `-X`/`-A`) and compute hashes or analyze with other tools.
8. Document commands, filters, timestamps, and why packets were flagged.

---

## My takeaway
Even at a base level, `tcpdump` is incredibly powerful: small, scriptable, and lightning-fast because of kernel-level BPF filtering. With a handful of flags and some UNIX tooling I can triage massive captures, hunt for suspicious flag patterns, and extract readable payloads. I plan to keep practicing tcpflag expressions and integrating `tcpdump` with `tshark` and text-processing pipelines for repeatable analysis.

---

*End of notes — saved as a Markdown file for my TryHackMe notes and GitHub learning log.*