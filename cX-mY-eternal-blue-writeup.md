---
title: "EternalBlue — TryHackMe Room Writeup"
author: "David Martinez"
date: 2025-11-05
tags: [TryHackMe, Windows, EternalBlue, Metasploit, post-exploitation, writeup]
---

# EternalBlue — TryHackMe (Writeup)

## Summary / TL;DR
This writeup documents my approach and findings for the **EternalBlue** TryHackMe room. I performed reconnaissance with Nmap (including vulnerability scripts), identified an exposed/winnable Windows service, exploited the vulnerability using Metasploit to gain an initial reverse shell, upgraded that shell to a Meterpreter session, migrated the Meterpreter process into an elevated `NT AUTHORITY\SYSTEM` process, and collected flags from `root`, `SAM`, and an `admin` user's documents. The lab reinforced how important patching and port hardening are, and it improved my practical familiarity with the Metasploit framework, Windows process navigation, and post-exploitation enumeration.

---

## Lab environment
- **Lab platform:** TryHackMe (EternalBlue room)
- **Target OS:** Windows (vulnerable to EternalBlue)
- **Attacker:** Kali / lab VM running Metasploit Framework
- **Notes:** This was performed in a contained, legal TryHackMe environment for educational purposes only.

---

## Objectives
1. Discover live hosts and open services on the target.
2. Identify vulnerabilities that may be exploitable (reconnaissance).
3. Exploit the identified vulnerability to obtain a shell.
4. Upgrade to a Meterpreter session and gain persistence/elevation where possible.
5. Enumerate the filesystem and sensitive artifacts and capture proofs (flags).
6. Document mitigation and lessons learned.

---

## Tools used
- `nmap` — for port scanning and vulnerability/script scanning
- `Metasploit Framework` — for exploitation and post-exploit modules
- Native Windows navigation commands (used from acquired shells) — for enumeration and flag collection
- (Local tooling) text editor for note taking and this writeup

> I avoided documenting exact exploit commands in this public writeup — if you are following along in a legal learning environment, refer to your lab instructions or sandboxed resources for precise command usage.

---

## Methodology (high-level)

### 1. Reconnaissance
- Performed an initial TCP port scan to discover which services were listening on the target.
- Re-ran targeted scans using Nmap scripting (`--script vuln` and other discovery scripts) to check for known service vulnerabilities and quickly identify likely attack vectors. The results highlighted an SMB service vulnerable to a known remote code execution issue (EternalBlue-style vulnerability).

### 2. Vulnerability confirmation
- Based on the Nmap output I confirmed that the target's SMB implementation returned signatures consistent with the vulnerable service/version. I cross-checked results to ensure this was a valid candidate for exploitation in the lab.

### 3. Exploitation
- Used the Metasploit Framework to stage an exploit that leveraged the identified SMB vulnerability. The framework generated and delivered a payload that opened a reverse connection back to my attacking host, resulting in an initial shell on the target.
- After the initial connection, I upgraded the session to a Meterpreter session to gain more reliable interactive control and additional post-exploitation capabilities.

> Note: This section purposefully omits low-level exploit commands and payload generation details because those are potentially dangerous outside safe lab contexts. In a sanctioned learning environment, follow your lab’s guidance and Metasploit documentation.

### 4. Post-exploitation & privilege escalation
- From the Meterpreter session I enumerated running processes and available privileges.
- I migrated the Meterpreter process into a process that had higher privileges (an `NT AUTHORITY\SYSTEM` process) to gain elevated capabilities and access to protected parts of the filesystem and registry.
- With elevated context, I explored the filesystem and user profiles to locate flags and sensitive artifacts.

### 5. Evidence collection
- Located and read flags / proof files placed in:
  - a root/system-level location
  - the Windows `SAM` area (or files representing SAM/credentials in the lab)
  - an admin user's documents folder
- Collected notes and saved the flag filenames/paths for reporting.

---

## Findings
- The target was running an SMB service susceptible to the EternalBlue-style vulnerability (unpatched SMB service).
- Exploitation path allowed remote code execution and delivery of a reverse shell.
- Using Meterpreter greatly improved interaction and post-exploit tooling (process migration, file enumeration, credential harvesting avenues inside the lab).
- After migrating to an elevated process, I had access to system resources and user data that were otherwise unavailable to the initial unprivileged shell.

**Flags captured (paths only, no raw contents):**
- `/root/flag.txt` (or equivalent system-level proof file)
- `C:\Windows\System32\config\SAM` (or lab-provided SAM proof artifact)
- `C:\Users\Administrator\Documents\flag.txt` (admin user document containing proof)

---

## Lessons learned
1. **Patch management matters** — Unpatched SMB left the system trivially compromiseable. Regular patching would have prevented the root cause of this lab.
2. **Minimize exposed services** — SMB should not be exposed unnecessarily, and network segmentation reduces blast radius.
3. **Metasploit familiarity** — Working with Metasploit improves speed during exploitation and post-exploit tasks, but it's critical to understand what each module and payload does to operate safely and ethically.
4. **Process migration and context** — Migrating into an elevated process is a powerful post-exploitation technique. It demonstrates how privilege context impacts access to files/credentials.
5. **Evidence collection & reporting** — Always record file paths, timestamps, and commands used (in a private notebook) during a test so your report is reproducible and verifiable.

---

## Mitigations / Recommendations
- **Apply vendor patches** for SMB and ensure operating systems are kept up to date.
- **Disable or restrict SMB** on endpoints that do not require it; use firewall rules to block SMB (TCP 139/445) from untrusted networks.
- **Use least privilege** — reduce the number of high-privilege service accounts and remove unnecessary local administrator accounts.
- **Network segmentation** — limit SMB access to trusted subnets and implement intrusion detection to spot anomalous SMB activity.
- **Patch validation & testing** — maintain an automated patch-testing pipeline that validates critical hosts for known-exploit exposure after updates are applied.
- **Monitoring & EDR** — enable endpoint detection/response tools to detect in-memory exploitation, suspicious process injection, and process migration activity.

---

## Appendix A — Suggested (private) notes to keep during labs
> For your own records (do not publish sensitive commands or payloads):
- Nmap command variations tried and output summaries.
- Exact Metasploit modules used and module options (in private personal notes only).
- Timestamps of successful connections and the process IDs before/after migration.
- File paths of found flags and any artifacts downloaded.
- Screenshots (saved privately) showing command outputs and proof of flags.

---

## Appendix B — References & further reading
- Metasploit Unleashed / official Metasploit docs (for safe, sandboxed learning)
- Vendor advisories and CVEs related to SMB/EternalBlue
- Defensive guidance on SMB hardening from trusted vendor KB articles

---

## Closing thoughts
This lab was a practical demonstration of how a single unpatched service can lead to full system compromise. It reinforced the need for multiple defensive layers: patching, network controls, least privilege, and monitoring. The hands-on exercise improved my operational comfort with the Metasploit Framework, Windows internals for lateral movement and privilege escalation, and how to document an engagement responsibly.
