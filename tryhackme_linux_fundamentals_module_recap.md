# TryHackMe: Linux Fundamentals (Module Recap)

## 🧰 Tools & Concepts Explored
- Terminal text editors
- General utilities (downloading files, serving content with a Python webserver)
- Processes and process management
- Crontabs (scheduling and automation)
- Package management
- Reviewing system logs

---

## 📘 What I Learned

### ✍️ Terminal Text Editors
- Practiced editing files directly in the terminal.
- Tools like `nano` and `vim` allow quick modifications without leaving the shell.

### ⚙️ General Utilities
- Learned how to download and serve content with a simple **Python webserver**:
  ```bash
  python3 -m http.server 8080
  ```
- Useful for testing, sharing files, or setting up lightweight services.

### 🧵 Processes
- Understood how to view and manage running processes.
- Commands:
  - `ps` – show processes.
  - `top` / `htop` – monitor active processes in real-time.
  - `kill` – stop processes by PID.

### ⏰ Crontabs & Automation
- Used **crontabs** to schedule recurring tasks (backups, updates, scripts).
- Syntax:
  ```bash
  * * * * * /path/to/script.sh
  ```
  (minute, hour, day, month, day-of-week)

### 📦 Package Management
- Learned to install, update, and remove packages with tools like `apt` (Debian/Ubuntu).
  - `apt update` – refresh package lists.
  - `apt upgrade` – update installed packages.
  - `apt install <package>` – install new packages.

### 📂 Reviewing Logs
- Explored system logs stored in `/var/log`.
- Logs are vital for troubleshooting, security monitoring, and system auditing.
- Learned to use commands like `less`, `tail -f`, and `grep` for log analysis.

---

## 🛠 Notes from My Experience
I ran into some issues during this module, but stayed consistent.  
One valuable learning moment was recognizing **when I had lost my remote connection**:  
- I had entered commands correctly, but they didn’t respond as expected.  
- By checking logs, I was able to confirm what was happening and locate the data I needed.  

This helped reinforce not only **Linux commands**, but also the **mindset of troubleshooting and persistence**.

---

## ✍️ Personal Takeaways
This module showed me the power of Linux — with relatively simple commands, you can achieve powerful results.  
From text editing and automation to log review and package management, I feel more confident in my ability to **administer, troubleshoot, and secure Linux systems**.  

---
