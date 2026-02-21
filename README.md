# 🔐 WebSocket Security Guide (CSWSH & Secure Implementation)

This repository explains common security risks in WebSocket implementations, with a focus on:

- Cross-Site WebSocket Hijacking (CSWSH)
- WebSocket handshake identification
- Security misconfigurations
- Secure implementation guidelines

⚠️ This guide is for educational and authorized security testing purposes only.

---

# 🧠 What is WebSocket?

WebSocket is a protocol that enables full-duplex communication between client and server over a single TCP connection.

Secure WebSocket uses:

```
wss://
```

which is WebSocket over TLS.

---

# 📡 Identifying a WebSocket Handshake

A WebSocket handshake is an HTTP request that upgrades the protocol.

It can be identified by these headers:

```
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Version
Sec-WebSocket-Key
```

Example handshake:

```
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Version: 13
Origin: https://example.com
```

If the server responds with:

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
```

the connection is successfully established.

---

# 🚨 Cross-Site WebSocket Hijacking (CSWSH)

## What is CSWSH?

CSWSH occurs when:

- The server relies only on session cookies
- The WebSocket handshake does not validate the `Origin` header
- No CSRF protection is implemented

If a victim is authenticated, a malicious site could attempt to open a WebSocket connection to the vulnerable server.

---

# ⚠️ Why It Happens

Common root causes:

- Missing `Origin` validation
- Cookie-based authentication without CSRF protection
- No token-based handshake verification
- Weak server-side validation

---

# 🔎 How to Test for CSWSH (Authorized Testing Only)

During a security assessment:

1. Capture the WebSocket handshake request.
2. Check if the server validates:
   - `Origin` header
   - Authentication tokens
   - CSRF tokens
3. Attempt to initiate a WebSocket connection from a different origin in a controlled lab environment.
4. Observe whether the connection is accepted.

If the server does not validate the origin and accepts cookie-based authentication blindly, it may be vulnerable.

---

# 🔐 How to Secure a WebSocket Connection

## 1️⃣ Use Secure Protocol

Always use:

```
wss://
```

Never use plain `ws://` in production.

---

## 2️⃣ Validate the Origin Header

On the server side:

- Check the `Origin` header
- Reject connections from untrusted domains

Example logic (conceptual):

```
if origin not in allowed_origins:
    reject_connection()
```

---

## 3️⃣ Use Token-Based Authentication

Avoid relying only on cookies.

Instead:

- Require a short-lived access token
- Validate the token during handshake
- Avoid automatic cookie authentication

---

## 4️⃣ Protect Against CSRF

- Include CSRF tokens in handshake parameters
- Validate tokens server-side
- Bind tokens to user sessions

---

## 5️⃣ Treat WebSocket Data as Untrusted

Data sent and received over WebSockets must be:

- Validated
- Sanitized
- Escaped before rendering

This prevents:

- SQL Injection
- XSS
- Command Injection
- Business logic abuse

---

## 6️⃣ Avoid User-Controlled WebSocket URLs

- Hardcode WebSocket endpoints
- Do not allow user-controlled data to define WebSocket targets

---

# 🛡 Secure Architecture Example

Recommended flow:

```
Client
   ↓
HTTPS Authentication
   ↓
Receive Short-Lived Token
   ↓
WebSocket Handshake with Token
   ↓
Server Validates:
    - Origin
    - Token
    - Permissions
```

---

# 📊 Common Vulnerability Mapping

- CWE-346 – Origin Validation Error
- CWE-352 – Cross-Site Request Forgery
- OWASP Top 10 – A01 Broken Access Control
- OWASP API Top 10 – Broken Authentication

---

# 🔍 Security Testing Checklist

- [ ] Using wss://
- [ ] Origin validation implemented
- [ ] No cookie-only authentication
- [ ] CSRF protection applied
- [ ] Input validation on messages
- [ ] Role-based authorization enforced
- [ ] No sensitive actions without revalidation

---

# 📚 Use Cases

- Web application VAPT
- Bug bounty testing
- Real-time application security
- API security reviews

---

# 📜 License

Educational and authorized security testing purposes only.

---

## ✅ End of Guide
