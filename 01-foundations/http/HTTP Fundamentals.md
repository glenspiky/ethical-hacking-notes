# 🌐 HTTP Fundamentals

> **HTTP (HyperText Transfer Protocol)** is a stateless, application-layer protocol used for communication between clients and servers on the web.

---

## 📌 1. What is HTTP?

- Protocol used to transfer data over the web
- Works on **Client–Server** model
- Stateless (each request is independent)
- Runs over **TCP** (HTTP/1.1, HTTP/2) or **QUIC/UDP** (HTTP/3)

---

## 🧠 2. How HTTP Works

1. Client sends an HTTP request
2. Server processes the request
3. Server sends an HTTP response
4. Connection is closed or kept alive
   DNS -> unencrypted

- **DNS**: Resolves domain → IP
- **TCP**: Establishes reliable connection (3-way handshake)
- **TLS**: Encrypts communication (HTTPS)
- **HTTP**: Actual request/response data

---

## 👤 Client vs 🖥 Server Responsibility

### Client-Controlled (UNTRUSTED)

- HTTP method
- URL path
- Headers
- Cookies
- Request body

> Everything visible in **Burp** is client-controlled.

### Server-Controlled (TRUST BOUNDARY)

- Authentication checks
- Authorization decisions
- Business logic
- Database access

---

## 🧪 Tool Boundaries

### 🦈 Wireshark

- Sees: DNS, TCP, TLS
- Cannot see HTTP on HTTPS
- Purpose: understand transport & encryption

### 🧰 Burp Suite

- Sees raw HTTP (after TLS is terminated)
- Acts as a MITM using trusted CA
- Purpose: analyze & manipulate application-layer data

---

## 🔐 Why Burp Can See HTTPS Traffic

- Browser trusts Burp’s CA certificate
- TLS is terminated at Burp
- Burp re-encrypts traffic to server

> HTTPS is about **trust**, not secrecy.

---

## 📜 Key HTTP Properties

- HTTP is **stateless**
- Server relies on:
  - Cookies
  - Tokens (e.g., JWT)
- Changing or removing these affects behavior

---

## 🧠 Security Insight (Core Idea)

> Bugs exist when the server **fails to properly validate or authorize client input**.

Burp helps:

- Observe inputs
- Modify requests
- Infer backend logic from responses

---

## 🧠 One-Line Summary

**Wireshark shows how data moves.  
Burp shows what the client asks.  
Security bugs live where the server trusts the client.**
