# TryHackMe — Active Directory Foundations (Lab write-up)

## Summary
I completed a TryHackMe room that introduced the foundational components and concepts of Active Directory (AD) and Windows domains. During the lab I worked with managing users and computers, creating Organizational Units (OUs), building Group Policy Objects (GPOs), and explored authentication methods including Kerberos. I also reviewed AD structure concepts such as trees and forests. This exercise helped me apply Windows domain administration concepts in a hands-on environment and reinforced how AD centralizes identity, policy, and access control in enterprise Windows networks.

---

## What I did (hands-on)
- Created multiple Organizational Units (OUs) to logically group users and computer accounts.
- Created and linked Group Policy Objects (GPOs) to OUs to enforce configuration and security settings.
- Managed user and computer accounts (creation, modification, and delegation of permissions).
- Investigated authentication methods used in Windows domains and dove into Kerberos ticketing behavior and how it ties to authentication and delegation.
- Reviewed higher-level AD structures: domains, trees, and forests—how they relate and why they matter for trust boundaries and replication.

---

## Technical details & examples

### Organizational Units (OUs)
I used OUs to separate accounts and policies for different teams and device classes. OUs help implement least-privilege admin delegation and make GPO scoping cleaner.

**Why it matters:** OUs are the unit of administration — they let administrators apply targeted policies and delegate admin tasks without giving higher-level domain admin rights.

**Commands / consoles used:**
- Active Directory Users and Computers (ADUC) GUI for creating OUs and accounts.
- PowerShell (examples):
```powershell
# Create an OU
New-ADOrganizationalUnit -Name "Engineering" -Path "DC=example,DC=local"

# Create a user
New-ADUser -Name "Alice Johnson" -SamAccountName "ajohnson" -AccountPassword (ConvertTo-SecureString "P@ssw0rd!" -AsPlainText -Force) -Enabled $true -Path "OU=Engineering,DC=example,DC=local"
```

### Group Policy Objects (GPOs)
I defined and linked GPOs to OUs to enforce settings such as password policies, Windows Firewall rules, or drive mappings. I practiced editing GPO settings and reviewing resultant set of policy (RSoP).

**Examples:**
- Using Group Policy Management Console (GPMC) to create, edit, and link GPOs.
- For testing, I applied a simple policy (e.g., set a logon banner or configure a scheduled task) and used `gpupdate /force` on a domain-joined machine to observe results.

### Authentication — Kerberos & other methods
I explored Windows authentication in domains and focused on Kerberos:
- Observed how Kerberos issues Ticket Granting Tickets (TGTs) and service tickets, and how the Key Distribution Center (KDC) on domain controllers is central to this flow.
- Practiced querying ticket caches (e.g., `klist`) and examined how tickets map to service principals.

**Why it matters:** Understanding Kerberos is essential for troubleshooting authentication issues, managing delegated credentials, and defending against some lateral-movement techniques used by attackers.

### Domain topology: Domains, Trees, and Forests
I reviewed how domains form the basic security boundary and how trees/forests provide hierarchical naming, replication topology, and trust relationships.

**Key points:**
- A domain is an administrative boundary with its own DCs and security policies.
- Trees group domains with contiguous namespace; forests hold multiple trees and provide the ultimate trust boundary in AD.

---

## Lessons learned
- AD is central to Windows enterprise security — small misconfigurations in GPOs or delegation can have broad impact.
- OUs and GPOs provide scalable control when designed with clear naming and delegation strategies.
- Kerberos fundamentals are practical to know: TGT lifecycle, service tickets, and how clock skew or SPN misconfigurations can break authentication.
- Hands-on practice (creating OUs, linking GPOs, querying Kerberos tickets) made theoretical concepts concrete.

---

## Key takeaways (how this adds value for employers)
- I can perform basic domain administration tasks: creating OUs, managing accounts, and applying GPOs to enforce policy across machines.
- I understand Windows authentication flows (Kerberos) and can inspect ticket caches and troubleshoot common authentication failures.
- I can reason about AD architecture (domains, trees, forests) and how design decisions affect replication, trust, and security posture.
- These skills translate directly to roles involving systems administration, Windows-focused SOC work, or infrastructure security—especially where identity and access control are critical.

---

## Next steps I plan to take
- Deepen Kerberos knowledge: practice hands-on with ticket forging detection, constrained delegation, and common Kerberos-related attacks (in safe lab environments).
- Explore AD attack/defense: simulate lateral movement techniques and corresponding defensive detections (event logs, detections for unusual Kerberos requests, etc.).
- Automate AD tasks with PowerShell: scripts for bulk user provisioning and safe GPO reporting.
- Document reproducible lab steps and add screenshots/screencasts to this write-up for a richer portfolio artifact.

---

## How to reproduce (quick steps)
1. Launch an AD lab VM or TryHackMe room with a domain controller and a Windows client.
2. Use ADUC or PowerShell to create an OU and a test user.
3. Create a GPO in GPMC, edit a simple setting (e.g., logon message), link it to the OU.
4. On the domain-joined client, run `gpupdate /force` and verify the policy applied.
5. Use `klist` on the client to view Kerberos tickets and observe behavior after logging off and back on.

---

If you'd like, I can:
- Expand this into a longer GitHub README with screenshots and PowerShell scripts.
- Convert it into a polished `.md` file with embedded images or step-by-step terminal transcripts for the repo.

