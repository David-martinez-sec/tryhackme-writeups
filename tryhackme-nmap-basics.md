# TryHackMe — Nmap: Notes & Quick-Reference

*First-person notes from completing the Nmap: The Basics room. I practiced host discovery, port scans, SYN-only and full TCP connect scans, service/version detection, OS fingerprinting, and tuning speed and verbosity.*

---

## Overview
I completed the TryHackMe Nmap basics room and practiced scanning networks and hosts to discover live systems, open ports, running services, and operating system details. This document captures what I learned in first person, plus a quick reference of useful commands and my investigation checklist.

---

## What I learned (high-level)
- Nmap is a powerful network scanner used to discover hosts, open ports, services, versions, and operating system information.
- There are multiple scan techniques (host discovery, SYN scan, TCP connect, UDP scan) with different trade-offs in stealth, completeness, and required privileges.
- Nmap offers timing templates (`-T0`..`-T5`) to control speed/stealth, and verbosity levels (`-v`, `-vv`) to see more output.
- OS detection (`-O`) and service/version detection (`-sV`) provide useful context but are not always 100% accurate; interpretation is important.
- Nmap can run NSE (Nmap Scripting Engine) scripts for further checks (`-sC` or `--script`).

---

## Core commands I used (my go-to commands)

### Host discovery (ping / ping-sweep)
- Ping scan (list hosts that respond):
```
nmap -sn 10.10.14.0/24
```
- Treat all hosts as online (skip host discovery):
```
nmap -Pn 10.10.14.5
```

### Port scanning
- SYN scan (stealthy, requires root):
```
sudo nmap -sS 10.10.14.5
```
- TCP connect scan (full handshake; no root needed):
```
nmap -sT 10.10.14.5
```
- Scan specific ports or ranges:
```
nmap -p 22,80,443 10.10.14.5
nmap -p 1-65535 10.10.14.5
```
- UDP scan:
```
sudo nmap -sU 10.10.14.5
```

### Service & version detection
- Probe open ports to identify services and versions:
```
sudo nmap -sV 10.10.14.5
```
- Aggressive scan (combines discovery, version detection, script scanning, and OS detection):
```
sudo nmap -A 10.10.14.5
```

### OS detection
- Attempt remote OS fingerprinting:
```
sudo nmap -O 10.10.14.5
```
- Combine OS detection with version detection:
```
sudo nmap -O -sV 10.10.14.5
```

### Output options (save results)
- Save in all common formats:
```
nmap -oA scan-results 10.10.14.5
```
- Grepable (legacy) output:
```
nmap -oG scan.grep 10.10.14.5
```

---

## Scan techniques & trade-offs (what I keep in mind)
- `-sS` (SYN scan): fast, stealthier than full connect because it doesn't complete TCP handshake; requires root.
- `-sT` (TCP connect): completes the handshake, more likely to be logged by the target; works without root.
- `-sU` (UDP scan): slow and noisy; many UDP services are rate-limited or filtered — expect false negatives.
- `-A`: convenient but noisy — it runs scripts, does version and OS detection, and increases visibility.
- `-Pn`: useful when ICMP is blocked or when scanning a single host you know is online.

---

## Speed and verbosity
- Timing templates (`-T0` .. `-T5`) control how aggressively Nmap scans:
  - `-T0` Paranoid — extremely slow, for IDS evasion
  - `-T1` Sneaky
  - `-T2` Polite — reduced bandwidth/scan rate
  - `-T3` Normal (default)
  - `-T4` Aggressive — faster, suitable for LAN
  - `-T5` Insane — very fast, likely noisy and unreliable on non-local networks
- Example: faster LAN scan:
```
sudo nmap -sS -T4 -p 1-1000 10.10.14.0/24
```
- Verbosity:
  - `-v` increases verbosity: shows progress and more intermediate results.
  - `-vv` increases it further.
  - `--reason` shows why Nmap thinks a port is open/filtered (useful for triage).

---

## Nmap Scripting Engine (NSE)
- Run default scripts (safe/basic checks):
```
sudo nmap -sC 10.10.14.5
```
- Run specific scripts or categories:
```
sudo nmap --script=http-enum 10.10.14.5
sudo nmap --script "default,safe" 10.10.14.5
```
- NSE can detect common misconfigurations, enumerate services, or attempt credential checking — but scripts may be noisy.

---

## Practical tips I learned / reminders to myself
- Use `-sS` for stealthy scans when I have privileges; use `-sT` when I do not.
- Start with `-sn` on a network to find live hosts, then pivot to targeted scans on live IPs.
- Use `-p` to limit ports and speed up scans when I know which services to check.
- Combine `-oA` to save results for later analysis and reproducibility.
- Always check timestamps and output verbosity to reference findings later.
- Remember legal/ethical boundaries — only scan networks I have authorization to test.

---

## Investigation checklist (Nmap-focused)
1. Decide scope and get authorization (important!).
2. Run a host discovery (`nmap -sn`) to list online hosts.
3. For each live host, run a SYN or connect scan for common ports (`-sS` or `-sT -p 1-1024`).
4. If ports are found open, run `-sV` and `-O` to learn services and OS.
5. Run relevant NSE scripts (`-sC` or `--script`) for further enumeration.
6. Save results (`-oA`) and note which hosts had interesting ports/services.
7. Document commands, timing templates used, and brief results (open ports, service versions, OS guess).

---

## My takeaway
Nmap is an essential reconnaissance tool — flexible, scriptable, and efficient. By combining host discovery, targeted port scans, version and OS detection, and the NSE, I can build an accurate picture of a network's attack surface. Tuning timing templates, verbosity, and output options keeps scans actionable and reproducible.

---

*End of notes — saved as a Markdown file for my TryHackMe notes and GitHub learning log.*