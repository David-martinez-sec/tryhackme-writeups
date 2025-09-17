# TryHackMe: Windows Fundamentals 1 & 2

## 🧰 Modules Completed
- **Windows Fundamentals 1**
- **Windows Fundamentals 2**

---

## 🔎 Overview
In these modules I refreshed and expanded my familiarity with the Windows operating system — from basic desktop navigation to system utilities and remote connectivity for lab access.

---

## 🧰 Tools & Concepts Explored
- Desktop environment & File Explorer
- User Account Control (UAC)
- Control Panel & Settings app
- Task Manager
- System utilities accessible via **System Configuration** and other entry points:
  - Registry Editor (`regedit`)
  - UAC settings
  - System Information (`msinfo32`)
  - Resource Monitor
  - Computer Management (Event Viewer, Disk Management, Services)
- VPN setup for lab access using VirtualBox + OpenVPN

---

## 📘 What I Learned

### Desktop & File System
- Navigated the desktop and File Explorer to find files and manage folders.
- Understood common file paths and where user vs. system files are stored.

### User Account Control (UAC)
- UAC helps prevent unauthorized changes by prompting for elevation when required.
- Learned how UAC impacts administrative tasks and why minimizing unnecessary elevation improves security.

### Control Panel & Settings
- Compared legacy **Control Panel** tools with the modern **Settings** app.
- Used both UAC and Settings to adjust system and privacy options.

### Task Manager
- Inspected running processes, resource consumption, and startup programs.
- Used Task Manager for basic troubleshooting and terminating unresponsive apps.

### System Utilities & Configuration Access
- Accessed advanced tools through **System Configuration**, Start Menu, Run dialog, and administrative consoles:
  - **Registry Editor (`regedit`)** — view and edit Windows registry keys (use with caution).
  - **System Information (`msinfo32`)** — comprehensive hardware and software inventory.
  - **Resource Monitor** — detailed CPU, memory, disk, and network usage.
  - **Computer Management** — central console for Event Viewer, Disk Management, and Services.

### VPN + VirtualBox + OpenVPN
- Configured a VPN to reach TryHackMe lab environments while running a VM in VirtualBox.
- Learned how to use **OpenVPN** client configuration files to connect the VM securely to remote lab networks.

---

## 🛠 Practical Tasks Performed
- Launched and used Task Manager to investigate high CPU or memory usage.
- Opened System Information and Resource Monitor for deeper diagnostics.
- Edited basic registry/viewed keys (read-only exploration unless elevation required).
- Set up OpenVPN in a VirtualBox guest to access TryHackMe virtual networks.

---

## ✍️ Notes & Takeaways
- Windows exposes many utilities for administration and troubleshooting — knowing multiple access methods (Start Menu, Run, Control Panel, System Configuration) speeds up investigation.
- UAC is an important security boundary; learning when to elevate and how to limit exposure is key for safe administration.
- VPNing a VirtualBox VM with OpenVPN is a practical solution for securely accessing remote lab environments.
- These modules strengthened my confidence in day-to-day Windows system administration and in preparing VMs for remote lab work.

---

## ✅ Next Steps I Plan to Take
- Practice deeper Event Viewer analysis for log-based troubleshooting.
- Build a small Windows lab VM snapshot workflow (backup, revert, test).
- Explore Windows Defender and built-in security tools (Windows Defender Firewall, Defender ATP basics).

