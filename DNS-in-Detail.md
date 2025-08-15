# TryHackMe: DNS in Detail

## 🧰 Tools Used
- `nslookup`
  - Supports specifying query type:
    ```bash
    nslookup --type={A|AAAA|CNAME|TXT|MX} <domain>
    ```

---

## 📘 What I Learned

### 🌍 What is DNS?
**Domain Name System (DNS)** is the "phonebook" of the internet, translating human-readable domain names into IP addresses so users don’t need to memorize numerical IPs.

---

### 🏷 Domain Structure
- **TLD (Top Level Domain)** – Rightmost part of a domain name.
  - **gTLD**: Generic top-level domain (e.g., `.com`, `.org`, `.net`).
  - **ccTLD**: Country code top-level domain (e.g., `.us`, `.uk`, `.jp`).
- **Second Level Domain (SLD)** – The actual name of the website.
  - Example: `google` in `google.com`.
  - Limited to **63 alphanumeric characters** plus hyphens (non-sequential).
- **Subdomain** – Leftmost part of the domain.
  - Example: `admin` in `admin.google.com`.
- **Full Domain Length Limit**: Maximum of **253 characters**.

---

### 📄 Common DNS Record Types
| Record Type | Purpose |
|-------------|---------|
| **A**       | Resolves to an IPv4 address. |
| **AAAA**    | Resolves to an IPv6 address. |
|
