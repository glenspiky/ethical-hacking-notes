# Day 2 — HTTP Methods & Semantics

## 🎯 Goal

Understand what each HTTP method **means**, when to use it, and the **security implications** of using it incorrectly.

---

## 🌐 What is an HTTP Method?

> An HTTP method defines the **action** the client wants the server to perform on a resource.
> --Are sometimes referred to as HTTP verbs

Think of methods as **verbs**, not just request types.

---

## 📌 Core HTTP Methods

1. **GET** -The GET method requests a representation of the specified resource. Requests using GET should only retrieve data and should not contain a request content.(**Safe:** ✅ )
2. **POST** -Submit data to the server(not ideopotent)(**Safe:** ✅ )
3. **PUT** -Used to update or create resource already on the server
4. **Delete** -Used to delete data
5. **PATCH** -Works like **PUT** Used to update a single attribute os a rresource

**Purpose:** Retrieve data  
**Has Body:** ❌ (theoretically no)  
**Safe:** ✅  
**Idempotent:** ✅
-a request is considerd ideopotent if the intended effect on the server of multiple identical request with that method is the same as the effect for a single such request of the request method defined by this specificatin,PUT DELETE

**Example:**

```http
GET /users/1 HTTP/1.1
```

## Why using GET for authentication is BAD

❌ Example (bad design)

```http
GET /login?username=admin&password=1234
```

Looks simple… but it’s a security nightmare.
1️⃣ URLs are logged everywhere
When you use GET, credentials live in the URL.
That URL gets stored in:

- Web server logs
- Reverse proxies (NGINX, Apache)
  Load balancers
- CDN logs
- WAF logs

📌 Anyone with log access can see passwords in plain text.

**2️⃣ Browser history saves it**
Your browser stores full URLs.
So now:

- Malware
- Shared computers
- can recover:

```http
https://example.com/login?username=admin&password=1234
```

**3️⃣ Referer header leaks credentials**
When the browser loads another resource after login:
Referer: https://example.com/login?username=admin&password=1234
That Referer is sent to:

- Analytics tools
- Ads
- Third-party APIs
- External CDNs

🔥 Your password just left your site.

**4️⃣ URLs are visible by design**
URLs are:

- Shown in the address bar
- Copied when you share a link
- Visible in screenshots
- Saved in bookmarks

Passwords should never be that visible.
**5️⃣ GET requests are cached**

Browsers, proxies, and CDNs may cache GET requests.
Worst case:
Another user receives a cached response
Auth data persists longer than intended

**6️⃣ GET is meant to be safe**
GET is defined as:
Safe → must not change server state
Logging in:
Creates a session
Sets cookies
Changes auth state
So using GET for login breaks HTTP semantics, which leads to bugs and exploits.

✅ **Why POST is the correct choice**
✔ Credentials go in the body
POST /login
Body is not logged in URLs
Not stored in history
Not leaked via Referer
Not cached by default
🚨 Important clarification

## As a tester, if I see:

GET /login?password=
I immediately check:
Logs
Referer leaks
Caching behavior
Replay attacks
That’s usually a high-severity finding.

