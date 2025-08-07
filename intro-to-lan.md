# TryHackMe: Intro to LAN

## 🧰 Tools & Concepts Explored
- LAN Topologies
- Switches vs Hubs
- Routers
- Subnetting
- ARP (Address Resolution Protocol)
- DHCP Process

---

## 📘 What I Learned

### 🖧 LAN Topologies

**Network topology** refers to how devices are connected in a local network.

- **Star Topology**
  - Devices connect to a central device (e.g., switch).
  - Pros: Easier to manage individually.
  - Cons: More cabling and specialized equipment; costly and harder to scale.

- **Bus Topology**
  - All devices share a single “backbone” cable.
  - Pros: Cost-effective.
  - Cons: Bottlenecks and performance issues common due to shared bandwidth.

- **Ring Topology**
  - Devices are connected in a circular loop.
  - Pros: Easy to troubleshoot; less cable needed; avoids major bottlenecks.
  - Cons: Inefficient—data must pass through multiple devices to reach its destination.

---

### 🔀 Switches vs Hubs

- **Switch**
  - Aggregates Ethernet-connected devices in a LAN.
  - Maintains a MAC address table to intelligently route traffic.
  - Only sends packets to the intended recipient.
  - More efficient and secure than a hub.

- **Hub**
  - Broadcasts incoming packets to **all** ports.
  - Inefficient and can cause traffic congestion.

> Switches and routers can be connected together to improve redundancy and scalability.

---

### 🌍 Routers

- Connect **multiple networks** together (e.g., LAN to Internet).
- Create and manage **routes** to deliver data between networks.

---

### 📐 Subnetting

- Divides a network into smaller logical networks (subnets).
- Uses a **subnet mask** to define network and host portions of an IP.

#### Key IP Concepts:
| Type                 | Example           | Description |
|----------------------|-------------------|-------------|
| Network Address      | `192.168.1.0`     | Start of the network |
| Host Address         | `192.168.1.1`     | Assigned to devices |
| Default Gateway      | `192.168.1.254`   | Used to route traffic out of the network |

**Benefits of Subnetting:**
- Improved **efficiency**, **security**, and **management** of network traffic.

---

### 📡 ARP (Address Resolution Protocol)

- Associates **IP addresses** with **MAC addresses**.
- Each device stores this mapping in an **ARP cache** (a local ledger).

#### ARP Process:
1. **ARP Request**: "Who has IP 192.168.1.1? Tell me your MAC."
2. **ARP Reply**: The device with that IP responds with its MAC.
3. The requester stores this mapping in its ARP cache.

> ARP is essential for tasks like printing, file sharing, and internal communication in LANs.

---

### 📥 DHCP (Dynamic Host Configuration Protocol)

Automatically assigns IP addresses to devices on a network.

#### DHCP 4-Step Handshake:
1. **Discover**: Client looks for DHCP server.
2. **Offer**: Server proposes an IP address.
3. **Request**: Client accepts the offered address.
4. **ACK**: Server confirms and assigns the address.

> Alternatively, IPs can be manually assigned (called **static IPs**).

---

## ✍️ Personal Takeaways

This room deepened my understanding of how LANs function and communicate internally. I now have a clearer picture of:
- How different topologies impact network performance and cost.
- The distinction between switches and routers.
- Why ARP and DHCP are foundational to everyday networking.
These fundamentals form the backbone of secure, reliable local networks—vital knowledge for anyone pursuing cybersecurity.

---

