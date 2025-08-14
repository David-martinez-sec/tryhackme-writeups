# TryHackMe: Extending Your Network

## 🧰 Concepts Explored
- Port Forwarding
- Firewalls (Stateful vs Stateless)
- VPN Technology
- Routers
- Switches (Layer 2 vs Layer 3)

---

## 📘 What I Learned

### 🌐 Port Forwarding
- **Purpose**: Allows applications and services to be accessible from the internet instead of only locally.
- Opens **specific ports** and directs external traffic to internal devices.
- **Configuration**: Done on the router.
- Without port forwarding, services remain **isolated** to the local network.

---

### 🔥 Firewalls
- Devices or software that control **inbound and outbound** traffic.
- Determine which devices or packets are allowed to pass through a network.

**Types of Firewalls**:
1. **Stateful**
   - Evaluates the **entire connection** state before allowing/denying traffic.
   - Consumes more resources.
   - If a connection from a host is deemed malicious, the **entire device** can be blocked.

2. **Stateless**
   - Uses **predefined rules** to filter individual packets.
   - Faster but less intelligent.
   - Effectiveness depends entirely on the quality of the rule set.

**Practical Task**:
- Blocked malicious traffic from sending data to a specific port.

---

### 🔒 VPN (Virtual Private Network)
- Creates a **secure, encrypted "tunnel"** between devices over the internet.
- Allows geographically separated networks (e.g., company offices) to act as a **single private network**.
- Benefits:
  - **Privacy** – Encryption protects data from sniffing.
  - **Anonymity** – Hides origin of traffic.
  - **Security** – Authentication prevents unauthorized access.

**VPN Technologies**:
- **PPP (Point-to-Point Protocol)**:
  - Provides authentication and encryption.
  - Works with public certificates and private keys, similar to SSH.
- **PPTP (Point-to-Point Tunneling Protocol)**:
  - Encapsulates PPP data for transmission over a network.
- **IPsec**:
  - Encrypts data using existing IP framework for secure delivery.

---

### 📡 Routers
- Connect networks and **route data** between them.
- Operate at **Layer 3** of the OSI model.
- Routing decisions consider:
  - **Distance**
  - **Reliability**
  - **Medium speed** (e.g., copper vs fiber optic)

---

### 🔀 Switches
- Dedicated devices to connect multiple endpoints within a network.
- **Layer 2 Switch**:
  - Operates at OSI Layer 2 (Data Link).
  - Sends frames directly to the correct device using MAC addresses.
- **Layer 3 Switch**:
  - Adds limited routing capabilities (operates at Layer 3).
  - Can create **VLANs** to segment devices within the same physical network for better security and management.

---

## ✍️ Personal Takeaways
This room tied together **network expansion techniques** with **security considerations**.  
I now have a stronger understanding of:
- How port forwarding makes services publicly accessible.
- The difference between **stateful** and **stateless** firewalls.
- VPNs not just for personal privacy, but as **corporate connectivity tools**.
- How switches and routers differ in their OSI layer operations.

---

