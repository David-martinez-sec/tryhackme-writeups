# TryHackMe: OSI Model

## 🧰 Concepts Explored
- OSI (Open Systems Interconnection) Model
- Data encapsulation
- OSI Layers and their functions
- TCP vs UDP
- Routing protocols (OSPF, RIP)

---

## 📘 What I Learned

### 🌍 OSI Model Overview
The **Open Systems Interconnection (OSI) Model** is a standardized framework for how networked devices send, receive, and interpret data.  
**Key benefit:** Devices with different hardware, software, or functions can still communicate seamlessly.

---

### 📦 Encapsulation
**Encapsulation** is the process of sending data through various layers of the OSI model, with each layer adding its own header or information to the packet.  
- As data travels **down** the OSI layers, each layer adds specific information.
- As data travels **up**, each layer removes and interprets the information it understands.

---

## 🪜 OSI Model Layers (Top → Bottom)

1. **Application Layer**
   - Manages **protocols and rules** for user interaction with data.
   - Examples: HTTP, FTP, SMTP.
   - Closest layer to the end user.

2. **Presentation Layer**
   - Ensures **data standardization and integrity**.
   - Acts as a **translator** between application data formats.
   - Examples: Encryption, compression, format conversion.

3. **Session Layer**
   - Creates, manages, and terminates **sessions** between devices.
   - Responsible for keeping track of active connections.
   - Opens a session at connection start, closes it when finished.

4. **Transport Layer**
   - Handles **end-to-end transmission** of data.
   - **TCP (Transmission Control Protocol):**
     - Maintains a constant connection during transmission.
     - Performs error checking and ensures packets are received **in order**.
   - **UDP (User Datagram Protocol):**
     - Sends data without requiring a connection.
     - No error correction—faster but less reliable.
   - Examples: TCP, UDP.

5. **Network Layer**
   - Routes data between devices and across networks.
   - Handles **IP addressing** and packet forwarding.
   - Uses routing protocols to determine optimal path:
     - **OSPF** (Open Shortest Path First) – chooses most direct route.
     - **RIP** (Routing Information Protocol) – chooses route based on hop count.

6. **Data Link Layer**
   - Uses **MAC addresses** for physical addressing.
   - Takes IP information from the Network Layer and maps it to hardware addresses.
   - Divided into:
     - **MAC sublayer** – hardware addressing.
     - **LLC sublayer** – flow control and error checking.

7. **Physical Layer**
   - The **actual hardware** and transmission medium (cables, switches, fiber, radio signals).
   - Responsible for converting data into electrical, optical, or radio signals.

---

## 🎮 Practical Component
- Played an interactive game that required identifying the correct OSI layer in order for various network actions.

---

## ✍️ Personal Takeaways
The OSI Model provides a **layered approach** to networking, making troubleshooting easier by isolating problems to specific layers.  
Understanding TCP vs UDP helps determine when **speed** or **reliability** is more important.  
The routing protocol examples (OSPF and RIP) show how networks determine the **path** data takes—critical for both performance and security.

---

