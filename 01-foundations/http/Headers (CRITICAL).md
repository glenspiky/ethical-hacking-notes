# Day 4 — HTTP Headers (CRITICAL)

## ⏱ Duration

5 Hours

## 🎯 Goal

Understand **which HTTP headers affect trust** and how manipulating them can lead to **auth bypass, CSRF, CORS bugs, cache issues, and parsing flaws**.

---

---

## What is access control?

-Access control is the application of constraints on who or what is authorized to perform actions or access resources. In the context of web applications, access control is dependent on authentication and session management:

- Authentication confirms that the user is who they say they are.
- Session management identifies which subsequent HTTP requests are being made by that same user.
  Access control determines whether the user is allowed to carry out the action that they are attempting to perform.

### Access control security models

**1️⃣ Discretionary Access Control (DAC)**

> Mental model:

- Users decide access

- Bug bounty angle:

- IDOR

- User-controlled object IDs

- Missing ownership checks

> Ask:

_“Can I access another user’s resource?”_

**2️⃣ Role-Based Access Control (RBAC)**

Simple idea:

-Your role decides what you can do.

> Common roles:

-user

-admin

-staff

-moderator

> How apps implement it:

-Role stored in DB

-Role checks in backend

> Bug bounty angle:

-Role escalation

-Admin-only endpoints accessible to normal users

-Client-side role enforcement

> Typical bug:

```http
POST /admin/deleteUser
```

Works even when logged in as a normal user.

Ask yourself:

_“Is the backend actually enforcing roles?”_

**3️⃣ Attribute-Based Access Control (ABAC)**
Simple idea:

Access depends on conditions, not just user or role.

> Common attributes:

-Time

-Status (paid / unpaid)

-Location

-Ownership + state

> Bug bounty angle (logic bugs):

-Bypass workflow steps

-Perform actions in wrong order

-Ignore business rules

> Typical bug:

-Refund without payment

-Cancel order after shipping

-Access resource outside allowed time

> Ask yourself:

_“What conditions should exist but don’t?”_

**🚨 What You Should Remember (Only This)**

-Most web bugs break DAC or RBAC

-High-value bugs often break ABAC

-Status code 200 instead of 403 = red flag

-Trust assumptions = attack surface

**Context-dependent access controls**

> Context-dependent access controls restrict access to functionality and resources based upon the state of the application or the user's interaction with it.
> Context-dependent access controls prevent a user performing actions in the wrong order.

## Examples of broken access controll

## 🧠 Mental Model

> Headers tell the server **who you are**, **where you’re from**, and **how to parse your request**.  
> Bugs happen when the server **trusts the wrong header**.

## 1️⃣ Authorization Header

### 📌 What it is

Used to prove identity (Bearer tokens, Basic auth, API keys).

### Vertical access controls

> Vertical access controls are mechanisms that restrict access to sensitive functionality to specific types of users.

### Horizontal access controls

> Horizontal access controls are mechanisms that restrict access to resources to specific users.

### Parameter-based access control methods

> Some applications determine the user's access rights or role at login, and then store this information in a user-controllable location. This could be:

-A hidden field.
-A cookie.
-A preset query string parameter.

> The application makes access control decisions based on the submitted value. For example:

```http
https://insecure-website.com/login/home.jsp?admin=true
https://insecure-website.com/login/home.jsp?role=1
```

-This approach is insecure because a user can modify the value and access functionality they're not authorized to, such as administrative functions.

### Horizontal privilege escalation

Horizontal privilege escalation occurs if a user is able to gain access to resources belonging to another user, instead of their own resources of that type

### Horizontal to vertical privilege escalation

> Often, a horizontal privilege escalation attack can be turned into a vertical privilege escalation, by compromising a more privileged user. For example, a horizontal escalation might allow an attacker to reset or capture the password belonging to another user. If the attacker targets an administrative user and compromises their account, then they can gain administrative access and so perform vertical privilege escalation.

### Referer-based access control

> Some websites base access controls on the Referer header submitted in the HTTP request. The Referer header can be added to requests by browsers to indicate which page initiated a request.

### 🧪 Hands-on

- Remove `Authorization` header
- Modify token value
- Replay request in Burp Repeater

### 🔍 Observe

- Status code change? (200 / 401 / 403)
- Does the request still succeed?

### 🧠 Notes

- Does the endpoint **require auth**?
- What happens when auth is missing?

```http
Authorization: Bearer <token>
```

3️⃣ Origin / Referer
📌 What it is

> Used mainly for CSRF protection.

🧪 Hands-on
Remove Origin

> Change Origin to trusted domain

-Modify Referer

> 🔍 Observe
> -Does the server block or allow?

Any CSRF token checks?

> Is trust based only on Origin/Referer?

> Weak CSRF protection?

-Origin: https://evil.com
-Referer: https://evil.com/page
