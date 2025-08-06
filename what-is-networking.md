# TryHackMe: What is Networking

## 🧰 Tools Used
- Terminal / Command Line (Linux or Windows)
- `ping`
- `macchanger` (Linux tool for spoofing MAC addresses)

---

## 📘 What I Learned

### 🌐 The Internet: A Network of Networks
- The Internet is a **massive interconnection of smaller networks**. Each device on this global system needs an address to communicate.
- **IP Addresses** (Internet Protocol addresses) identify devices on a network. These are like mailing addresses for devices.

### 📦 IPv4 vs IPv6
- IPv4 has a limited number of addresses (around 4.3 billion). With the explosion of internet-connected devices, this isn’t enough.
- **IPv6** was introduced to handle the demand — it supports **over 340 undecillion** addresses, effectively future-proofing the system.

### 🕵️ MAC Addresses
- A **MAC (Media Access Control) address** is a hardware identifier that’s **unique to each network interface card (NIC)**.
- Think of a MAC address like a **fingerprint** — it doesn’t change unless manually spoofed.
- **MAC spoofing** can be used in attacks or bypasses, such as:
  - Gaining access to MAC-restricted networks
  - Mimicking another device on the network

### 🛰 Ping and ICMP
- `ping` is a common command-line tool that uses **ICMP (Internet Control Message Protocol)** to send packets to another device.
- It helps determine if another device is reachable and how long it takes for packets to travel (latency).
- Useful for diagnosing network issues or verifying connectivity.

---

## 🔧 What I Did

- ✅ **Spoofed a MAC address** using `macchanger` to gain access to a MAC-filtered network or website.
    ```bash
    sudo ifconfig eth0 down
    sudo macchanger -r eth0  # Random MAC
    sudo ifconfig eth0 up
    ```
- ✅ **Used `ping` to test connectivity** to a specific IP address.
    ```bash
    ping 8.8.8.8  # Google's DNS server
    ```

---

## 💡 Key Concepts

| Term         | Description |
|--------------|-------------|
| **IP Address** | Logical address for devices to communicate on a network |
| **IPv6**       | Next-gen IP format with vast address space |
| **MAC Address**| Physical, unique address tied to a device's NIC |
| **Ping**       | Tool to check reachability and latency using ICMP |
| **Spoofing**   | Falsifying network identity (like MAC) to bypass restrictions |

---

## ✍️ Personal Takeaways
This room helped me understand the foundation of how devices talk to each other on a network. It was especially eye-opening to see how easily MAC addresses can be spoofed, which emphasizes the importance of **layered security** — relying solely on MAC filtering is not enough.

Learning to use tools like `ping` and `macchanger` is a step toward gaining fluency in networking and security basics, both essential for a cybersecurity career.

---

