# TryHackMe: Packets and Frames

## 🧰 Concepts Explored
- Packets vs Frames
- TCP/IP Model vs OSI Model
- TCP vs UDP
- Ports & Common Port Numbers
- Netcat usage for manual connections

---

## 📘 What I Learned

### 📦 Packet vs Frame

- **Packet**:
  - Exists at **Layer 3 (Network Layer)** of the OSI model.
  - Contains the **IP header** and **payload** (data).
  - Comparable to the **letter** inside an envelope.

- **Frame**:
  - Exists at **Layer 2 (Data Link Layer)** of the OSI model.
  - Encapsulates the packet and adds **MAC address** and other info.
  - Comparable to the **envelope** that holds the letter.

---

### 📨 Common Packet Headers (IP)
- **Time To Live (TTL)** – Prevents endless routing loops by setting an expiration counter.
- **Checksum** – Verifies integrity; if data is altered, the checksum changes.
- **Source Address** – IP of the sender.
- **Destination Address** – IP of the receiver.

---

### 🖧 TCP/IP Model Overview
TCP/IP is a condensed version of the OSI model, consisting of 4 layers:
1. **Application**
2. **Transport**
3. **Internet**
4. **Network Interface**

---

### 🔄 TCP (Transmission Control Protocol)
- **Connection-based** protocol.
- Uses a **3-way handshake** to ensure reliability before sending data.
- Trades speed for **data integrity** and **order**.

#### Common TCP Headers:
- **Source Port** – Port opened by sender.
- **Destination Port** – Target port for the service or app.
- **Source IP** – Sender’s IP address.
- **Destination IP** – Receiver’s IP address.
- **Sequence Number** – Random number for the first data packet.
- **ACK Number** – Next expected sequence number (Seq + 1).
- **Checksum** – Ensures packet integrity.
- **Data** – Actual bytes of the message/file.
- **Flags** – Control data flow and connection state.

#### TCP 3-Way Handshake:
1. **SYN** – Client initiates connection.
2. **SYN/ACK** – Server acknowledges and responds.
3. **ACK** – Client confirms; connection is established.
4. **DATA** – Data transfer begins.
5. **FIN** – Gracefully close connection.
6. **RST** – Abruptly terminate connection due to an error.

---

### ⚡ UDP (User Datagram Protocol)
- **Connectionless** and stateless.
- Much faster than TCP, but no guaranteed delivery or order.
- Still includes headers like:
  - TTL
  - Source & Destination Address
  - Source & Destination Port
  - Data
- **No checksum required** (though some implementations still use one).

---

### 🔌 Ports
Ports act as **communication endpoints** for network services.  
Range: `0 – 65535`.  
Some ports are **reserved** for specific services:

| Service | Port | Description |
|---------|------|-------------|
| **FTP**  | 21   | File Transfer Protocol |
| **SSH**  | 22   | Secure Shell (text-based secure login) |
| **HTTP** | 80   | Web browsing (unencrypted) |
| **HTTPS**| 443  | Secure web browsing (encrypted) |
| **SMB**  | 445  | File & device sharing (e.g., printers) |
| **RDP**  | 3389 | Remote Desktop Protocol (visual login) |

---

## 🛠 Task Performed
- Used **Netcat (`nc`)** to manually connect to an IP address and a designated port.
    ```bash
    nc <IP_ADDRESS> <PORT>
    ```
  - Example:
    ```bash
    nc 192.168.1.10 80
    ```
  - This allowed me to interact directly with a service running on that port.

---

## ✍️ Personal Takeaways
This section clarified the **difference between packets and frames** and reinforced how TCP and UDP serve different networking needs.  
Learning Netcat provided a **hands-on method** to directly connect to services, a useful skill for both troubleshooting and penetration testing.

---

