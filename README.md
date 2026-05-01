# 🍪 Cookies, Sessions & Browser Storage

This guide explains **cookies**, **browser storage**, and authentication state management using **server-side sessions** and **JWTs**.

---

# 📌 What Are Cookies?

A **cookie** is small data stored in the browser and sent automatically with future requests to the same website.

Used for:

- Login sessions
- Preferences
- Tracking
- Shopping carts
- CSRF protection tokens

Example:

```http
Set-Cookie: sessionid=abc123
````

Browser later sends:

```http id="0s1dw9"
Cookie: sessionid=abc123
```

---

# 📌 a. Cookie Attributes

Cookie attributes control security and behavior.

---

## 🔹 Secure

### What It Does

Cookie is sent **only over HTTPS**.

### Example

```http id="80uvq8"
Set-Cookie: sessionid=abc123; Secure
```

### Security Benefit

Prevents cookie exposure over plain HTTP.

### Best Practice

Use `Secure` on all authentication cookies.

---

## 🔹 HttpOnly

### What It Does

Blocks JavaScript access to cookie.

Example:

```javascript
document.cookie
```

Cannot read HttpOnly cookies.

### Example

```http id="m7kr8w"
Set-Cookie: sessionid=abc123; HttpOnly
```

### Security Benefit

Reduces risk of stolen cookies through XSS.

### Best Practice

Use `HttpOnly` for session cookies.

---

## 🔹 SameSite

Controls whether cookies are sent in **cross-site requests**.

Helps reduce **CSRF attacks**.

---

### Values

## SameSite=Strict

Cookie sent only for same-site navigation.

Most secure, strict behavior.

```http id="p8b0f0"
Set-Cookie: sessionid=abc123; SameSite=Strict
```

---

## SameSite=Lax

Cookie sent on top-level navigation, but restricted on some cross-site requests.

Often a practical default.

```http id="c3g8qz"
Set-Cookie: sessionid=abc123; SameSite=Lax
```

---

## SameSite=None

Cookie allowed in cross-site requests.

Must also use `Secure`.

```http id="1p4xqe"
Set-Cookie: sessionid=abc123; SameSite=None; Secure
```

Used for:

* Third-party login flows
* Cross-domain embedded apps

---

# Security Summary

| Attribute | Purpose            | Security Benefit |
| --------- | ------------------ | ---------------- |
| Secure    | HTTPS only         | Prevent sniffing |
| HttpOnly  | No JS access       | Reduce XSS theft |
| SameSite  | Cross-site control | Reduce CSRF      |

---

# Recommended Session Cookie

```http id="5d2t0m"
Set-Cookie: sessionid=abc123; Secure; HttpOnly; SameSite=Lax
```

---

# 📌 b. Cookies vs localStorage vs sessionStorage

All store data in browser, but behavior differs.

---

# 🔹 Cookies

### Features

* Small storage (about 4KB)
* Automatically sent with HTTP requests
* Can expire
* Supports security flags

### Best For

* Login sessions
* CSRF tokens
* Small preference values

---

# 🔹 localStorage

### Features

* Larger storage (5MB+ typical)
* Persists after browser close
* Not automatically sent to server
* Accessible via JavaScript

### Example

```javascript id="vpk2u8"
localStorage.setItem("theme", "dark");
```

### Best For

* UI preferences
* Cached app state
* Non-sensitive data

### Risk

Can be stolen by XSS if page compromised.

---

# 🔹 sessionStorage

### Features

* Same API as localStorage
* Exists only for current browser tab/session
* Cleared when tab closes
* Not shared across tabs

### Example

```javascript id="d9u0tt"
sessionStorage.setItem("step", "2");
```

### Best For

* Temporary form progress
* Tab-specific state
* Short-lived UI data

---

# Comparison Table

| Feature                      | Cookies  | localStorage | sessionStorage |
| ---------------------------- | -------- | ------------ | -------------- |
| Sent to Server Automatically | Yes      | No           | No             |
| Accessible by JS             | Yes*     | Yes          | Yes            |
| HttpOnly Option              | Yes      | No           | No             |
| Persists Browser Restart     | Optional | Yes          | No             |
| Size                         | Small    | Larger       | Larger         |
| Best for Auth Session        | Yes      | Usually No   | No             |

* Unless `HttpOnly`

---

# Security Guidance

## Sensitive Tokens

Prefer secure HttpOnly cookies instead of localStorage.

## Non-Sensitive Preferences

Use localStorage.

## Temporary Per-Tab State

Use sessionStorage.

---

# 📌 c. Server-Side Sessions vs JWT

Used to maintain authenticated user state.

---

# 🔹 Server-Side Sessions

## How It Works

1. User logs in
2. Server creates session record
3. Session ID stored in cookie
4. Server checks session each request

```text id="ej14mu"
Browser cookie → sessionid=abc123
Server DB/Memory → user data
```

---

## Advantages

* Easy logout / revoke instantly
* Sensitive data stays server-side
* Simpler permission updates
* Strong control by backend

## Disadvantages

* Requires server storage
* Harder horizontal scaling without shared store

---

## Best Use Cases

* Traditional web apps
* Admin panels
* High-control environments
* Stateful applications

---

# 🔹 JWT (JSON Web Token)

## How It Works

Server issues signed token containing claims.

Client sends token each request:

```http id="d1ksp9"
Authorization: Bearer eyJhbGciOi...
```

Server validates signature.

---

## Advantages

* Stateless authentication
* Good for APIs
* Easy microservices integration
* Scales well across servers

## Disadvantages

* Harder immediate revocation
* Token leakage risk
* Claims may become stale
* Must manage expiry carefully

---

## Best Use Cases

* Mobile apps
* SPA frontends + APIs
* Distributed systems
* Microservices

---

# Sessions vs JWT Comparison

| Feature               | Server Session     | JWT       |
| --------------------- | ------------------ | --------- |
| Server Storage Needed | Yes                | No        |
| Easy Logout           | Yes                | Harder    |
| Horizontal Scaling    | Needs shared store | Easy      |
| Best for Web Login    | Excellent          | Good      |
| Best for APIs         | Good               | Excellent |

---

# Best Practices

## For Sessions

* Use secure HttpOnly cookies
* Rotate session IDs after login
* Expire idle sessions
* Store in Redis/DB if scaled

## For JWT

* Short token expiry
* Use refresh tokens carefully
* Sign strongly
* Validate issuer/audience
* Store safely (prefer HttpOnly cookie when possible)

---

# Practical Recommendation

## Use Server Sessions When:

* Building normal website login systems
* Need instant logout
* Need tighter backend control

## Use JWT When:

* Building APIs for mobile/web apps
* Multiple services need auth
* Stateless scaling matters

---

# Final Summary

## Cookies

Store small browser data; ideal for sessions with security flags.

## Storage APIs

* localStorage = persistent client data
* sessionStorage = temporary tab data

## Auth State

* Sessions = server-managed
* JWT = token-managed

Understanding these is essential for:

* Web Security
* Authentication Design
* Secure Frontend Development
* SOC / Blue Team Analysis

---

```
```
