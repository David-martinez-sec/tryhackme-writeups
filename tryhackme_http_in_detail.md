
# TryHackMe: HTTP in Detail

## 🧰 Tools & Concepts Explored
- HTTP vs HTTPS
- URL structure
- HTTP methods
- Status codes
- Headers (request and response)
- Cookies
- Practical HTTP requests (GET, POST, PUT, DELETE)

---

## 📘 What I Learned

### 🌍 HTTP vs HTTPS
- **HTTP**: A protocol defining rules for communication with web servers.
- **HTTPS**: Secure version of HTTP.
  - Encrypts data to prevent sniffing.
  - Ensures communication is with the authentic server via certificates.

---

### 🔗 URL Structure
A **Uniform Resource Locator (URL)** provides instructions for accessing resources on the web.  

**Components**:
- **Scheme**: Protocol used (e.g., `http`, `https`).
- **User**: Authentication info if required.
- **Host**: Domain name or IP address.
- **Port**: Network port (default: `80` for HTTP, `443` for HTTPS).
- **Path**: File name or resource location.
- **Query String**: Extra info (parameters) for acquiring the resource.
- **Fragment**: Reference to a location on the page.

---

### 📄 Example HTTP Request
```http
GET / HTTP/1.1
Host: tryhackme.com
User-Agent: Mozilla/5.0 Firefox/87.0
Referer: https://tryhackme.com/
```

### 📄 Example HTTP Response
```http
HTTP/1.1 200 OK
Server: nginx/1.15.8
Date: Fri, 09 Apr 2021 13:34:03 GMT
Content-Type: text/html
Content-Length: 98

<html>
<head>
    <title>TryHackMe</title>
</head>
<body>
    Welcome To TryHackMe.com
</body>
</html>
```

---

### 🛠 HTTP Methods
- **GET** – Retrieve data.
- **POST** – Submit data.
- **PUT** – Update data.
- **DELETE** – Remove data.

---

### 📊 HTTP Status Codes
Status codes tell the client the **result** of their request.

- **100s** – Informational (request accepted in part).
- **200s** – Success.
- **300s** – Redirection.
- **400s** – Client errors.
- **500s** – Server errors.

**Common Examples**:
- `200 OK` – Successful request.
- `201 Created` – Resource created (e.g., new user).
- `301 Moved Permanently` – Redirect to a new location.
- `302 Found` – Temporary redirect.
- `400 Bad Request` – Malformed or incomplete request.
- `401 Unauthorized` – Authentication required.
- `403 Forbidden` – Access denied.
- `404 Not Found` – Resource doesn’t exist.
- `405 Method Not Allowed` – Wrong method used.
- `500 Internal Server Error` – Server-side failure.
- `503 Service Unavailable` – Overloaded or maintenance.

---

### 📩 Headers

#### Request Headers
Sent from the **client** to the server:
- **Host** – Specifies the website requested.
- **User-Agent** – Identifies the client/browser software.
- **Content-Length** – Tells server how much data to expect.
- **Accept-Encoding** – Informs server of supported compression methods.
- **Cookie** – Sends saved data back to server.

#### Response Headers
Returned from the **server** to the client:
- **Set-Cookie** – Sends cookie data to be stored by client.
- **Cache-Control** – Defines how long content should be cached.
- **Content-Type** – Defines the type of data returned (e.g., HTML, JSON).
- **Content-Encoding** – Compression method used (e.g., gzip).

---

### 🍪 Cookies
- Small pieces of data stored on the client’s computer.
- Created when server sends a **Set-Cookie** header.
- Sent back to the server with each request.
- Provides state in an otherwise **stateless HTTP protocol**.
- Used for logins, session tracking, and personalization.

---

## 🛠 Practical Task
- Sent HTTP requests using:
  - **GET** – Fetch a resource.
  - **POST** – Submit data to the server.
  - **PUT** – Update an existing resource.
  - **DELETE** – Remove a resource.

---

## ✍️ Personal Takeaways
This room clarified how **web communication works under the hood**.  
Understanding **requests, responses, headers, and status codes** gives me the ability to troubleshoot web applications, test APIs, and recognize common misconfigurations.  

The practical task reinforced how each HTTP method interacts differently with the server, a key skill for penetration testing and web app security.

---
