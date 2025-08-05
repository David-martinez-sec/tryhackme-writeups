# TryHackMe – Introduction to Offensive Security

🗂️ Room Link: [https://tryhackme.com/room/introductiontooffensivesecurity](https://tryhackme.com/room/introductiontooffensivesecurity)  
📅 Date Completed: 8-5-2025

---

## 🧠 Overview

This room provides an interactive introduction to **offensive security**—the proactive side of cybersecurity where professionals simulate real-world attacks to find and fix vulnerabilities before malicious actors do.

The hands-on scenario walks you through attacking a simulated banking website, discovering hidden web directories using **Gobuster**, and exploiting an **admin panel** to simulate transferring money from a user account.

---

## 🎯 Learning Objectives

- Understand what offensive security professionals do
- Learn how attackers discover hidden directories using tools like Gobuster
- Simulate exploiting a web-based vulnerability to access an admin portal
- Recognize the importance of securing backend interfaces and login portals

---

## 🧪 Tools Used

| Tool       | Purpose                                                  |
|------------|----------------------------------------------------------|
| **Gobuster** | Brute-forces web directories to discover hidden paths on a server |

---

## 🛠️ Step-by-Step Walkthrough

### 1. Initial Reconnaissance  
I started by visiting the website’s main page and observed the front-end content. It didn’t show any obvious login or admin options.

### 2. Directory Brute-Forcing with Gobuster  
I used `Gobuster` to enumerate hidden paths on the web server.  
Command used:
```bash
gobuster dir -u http://<target-ip> -w /usr/share/wordlists/dirb/common.txt
```

This scan revealed a hidden `/admin` directory, which wasn’t linked anywhere on the public website. This is a common misconfiguration and potential attack surface.

### 3. Gaining Unauthorized Access  
I navigated to the `/admin` page. Using provided hints and default credentials, I successfully logged in to the admin portal.

### 4. Simulated Exploit – Transferring Money  
Once inside the admin interface, I accessed user account details and used a simulated interface to transfer funds from another account. This demonstrated the risks of exposed admin panels and weak credential protections.

---

## 📚 Key Concepts Learned

- **Offensive security** is about proactively testing and exploiting systems to find weaknesses before real attackers do.
- **Gobuster** is a powerful tool for **directory brute-forcing**, allowing attackers (or security professionals) to discover admin pages, config files, login portals, etc.
- **Admin panels are often hidden rather than protected**, making them easy targets if discovered.
- Even simple web applications can contain vulnerabilities that allow attackers to perform serious actions, like modifying account balances.

---

## ✍️ Personal Takeaways

This was my first exposure to using Gobuster, and it immediately clicked how valuable directory discovery is for attackers. I realized that many websites expose more than they intend just by using predictable paths like `/admin`, `/login`, or `/config`. Offensive security isn’t just about breaking in—it’s about noticing what others miss. This room helped me connect theory to practice, especially in understanding how attackers move from discovery to exploitation.

---

## 📁 Next Steps

- Try rooms like **Basic Pentesting**, **OWASP Top 10**, or **Vulnversity** to expand web exploitation skills
- Learn to use **Burp Suite** for deeper web app testing
- Review defensive logs (later modules) to see how brute-force attempts might be detected
- Write a small guide on protecting admin portals using access controls, obscure paths, and rate limiting

---

## ✅ Tags

`TryHackMe` `Gobuster` `Web Exploitation` `Admin Portal` `Offensive Security` `Directory Brute Force` `CTF Walkthrough`
