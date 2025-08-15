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
| **CNAME**   | Alias for another domain name. Requires an additional DNS lookup to resolve the target’s IP. |
| **MX**      | Mail exchange record – points to mail servers for the domain. |
| **TXT**     | Stores arbitrary text data; often used for email verification (e.g., SPF, DKIM). |

---

### 🔄 How a DNS Request Works
1. **Check Local Cache** – Browser/OS checks if the answer is already stored locally.
2. **Recursive DNS Server Query** – Sends request to the configured DNS server.
3. **Root DNS Server** – Directs to the appropriate TLD server.
4. **TLD Server** – Directs to the domain’s authoritative server.
5. **Authoritative DNS Server** – Provides the final IP or record.
6. **Response & Caching** – Recursive server caches the record for the specified **TTL** (Time To Live).

---

### 🛠 Practical Task
- Made DNS queries and examined results:
    ```bash
    nslookup --type=A tryhackme.com
    nslookup --type=AAAA tryhackme.com
    nslookup --type=CNAME store.tryhackme.com
    nslookup --type=TXT tryhackme.com
    nslookup --type=MX tryhackme.com
    ```

---

## ✍️ Personal Takeaways
Understanding DNS structure and query flow is crucial for:
- Identifying misconfigurations.
- Investigating phishing or spoofing attempts.
- Troubleshooting connectivity issues.
Learning to use `nslookup` to directly query DNS records adds a **hands-on investigative skill** to my networking toolkit.
