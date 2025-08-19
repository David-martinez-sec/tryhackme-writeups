# TryHackMe: Linux Fundamentals 2

## 🧰 Tools & Concepts Explored
- Remote access with SSH
- Using command flags, switches, and manuals
- Common filesystem interaction commands
- File permissions & switching users
- Linux root directory structure

---

## 📘 What I Learned

### 🔑 Connecting with SSH
- Learned how to remotely connect to a Linux machine using **SSH (Secure Shell)**.
- Provides a secure, encrypted way to access and administer remote systems.

---

### ⚙️ Command Flags & Switches
- Expanded basic commands by using **flags/switches** for more control.
- Example:
  - `ls -l` → list files in long format
  - `ls -a` → include hidden files
- Learned to use **man pages** (`man <command>`) as built-in documentation for commands.

---

### 📂 Filesystem Interaction
- Practiced commands to interact with files and directories more efficiently.
- Examples:
  - `cp` – copy files.
  - `mv` – move or rename files.
  - `rm` – remove files.
  - `cat`, `less`, `head`, `tail` – read contents of files.

---

### 🔐 File Permissions & Users
- Brief intro to Linux file permission structure:
  - Read (`r`), Write (`w`), Execute (`x`).
  - Permissions grouped by **owner**, **group**, and **others**.
- Learned to **switch users** using `su` or `sudo` for elevated privileges.

---

### 📂 Important Root Directories (Ubuntu Linux)
- `/bin` – Essential binary executables.
- `/etc` – System configuration files.
- `/home` – User home directories.
- `/var` – Variable data (logs, caches).
- `/tmp` – Temporary files.
- `/usr` – User-installed applications and binaries.
- `/root` – Home directory for the root user.

> Understanding the purpose of these directories is important for **administration and investigation** during security analysis.

---

## ✍️ Personal Takeaways
This room built on the basics by introducing **remote management with SSH**, a deeper dive into **command flexibility**, and **permissions**.  
Learning the **Linux directory structure** gave me a foundation for navigating and extracting relevant information, a crucial skill for cybersecurity work.

---
