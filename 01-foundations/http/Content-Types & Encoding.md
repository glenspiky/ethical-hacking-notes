# Day 5 — Content-Types & Encoding

## 🎯 Goal

Understand how different `Content-Type` headers affect request parsing, backend behavior, and security.

---

## 🧠 Key Concepts

### Content-Type

- Defines how the request body should be interpreted by the server.
- Mismatch between body and `Content-Type` can cause logic bugs or security issues.
- The HTTP Content-Type representation header is used to indicate the original media type of a resource before any content encoding is applied.
  -In responses, the Content-Type header informs the client about the media type of the returned data. In requests such as POST or PUT, the client uses the Content-Type header to specify the type of content being sent to the server. If a server implementation or configuration is strict about content type handling, a 415 client error response may be returned.

-The Content-Type header differs from Content-Encoding in that Content-Encoding helps the recipient understand how to decode data to its original form.

> Note: This value may be ignored if browsers perform MIME sniffing (or content sniffing) on responses. To prevent browsers from using MIME sniffing, set the X-Content-Type-Options header value to nosniff

## -MIME (Multipurpose Internet Mail Extensions) in HTTP is a standard that identifies the format of files, documents, or data transmitted over the internet, allowing browsers to handle content correctly.

### Content-Type in multipart forms

> In a POST request resulting from an HTML form submission, the Content-Type of the request is specified by the enctype attribute on the <form> element.

```html
<form action="/foo" method="post" enctype="multipart/form-data">
  <input type="text" name="description" value="Description input value" />
  <input type="file" name="myFile" />
  <button type="submit">Submit</button>
</form>
```

```http
POST /foo HTTP/1.1
Content-Length: 68137
Content-Type: multipart/form-data; boundary=ExampleBoundaryString

--ExampleBoundaryString
Content-Disposition: form-data; name="description"

Description input value
--ExampleBoundaryString
Content-Disposition: form-data; name="myFile"; filename="foo.txt"
Content-Type: text/plain

[content of the file foo.txt chosen by the user]
--ExampleBoundaryString--
```

## 📦 Content-Types

### 1️⃣ application/json

**Description**

- Sends structured data as JSON.
- Common in APIs.

**Example**

```http
Content-Type: application/json

{
  "username": "admin",
  "password": "123456"
}
```

### Content-Type in URL-encoded form submission

When forms don't involve file uploads and are using simpler fields, URL-encoded forms may be more convenient where the form data is included in the request body:

```html
<form action="/submit" method="post">
  <label for="comment">Comment:</label>
  <input type="text" id="comment" name="comment" value="Hello!" />
  <button type="submit">Submit</button>
</form>
```

```htttp
POST /submit HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

comment=Hello!
```

### Content-Type in a REST API using JSON

Many REST APIs use application/json as a content type which is convenient for machine-to-machine communication or programmatic interaction. The following example shows a 201 Created response showing the result of a successful request:

```http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "message": "New user created",
  "user": {
    "id": 123,
    "firstName": "Glen",
    "lastName": "Barasa",
    "email": "glen1.com"
  }
}
```

## What is HTTP request smuggling?

-HTTP request smuggling is a technique for interfering with the way a web site processes sequences of HTTP requests that are received from one or more users

### How do HTTP request smuggling vulnerabilities arise?

-Most HTTP request smuggling vulnerabilities arise because the HTTP/1 specification provides two different ways to specify where a request ends: the **Content-Length header** and the **Transfer-Encoding header**.
-The Content-Length header is straightforward: it specifies the length of the message body in bytes

-The Transfer-Encoding header can be used to specify that the message body uses chunked encoding. This means that the message body contains one or more chunks of data. Each chunk consists of the chunk size in bytes (expressed in hexadecimal), followed by a newline, followed by the chunk contents. The message is terminated with a chunk of size zero. For example:

```http
POST /search HTTP/1.1
Host: normal-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 11
q=smuggling
```

```http
POST /search HTTP/1.1
Host: normal-website.com
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

b
q=smuggling
0
```

> Many security testers are unaware that chunked encoding can be used in HTTP requests, for two reasons:

1. Burp Suite automatically unpacks chunked encoding to make messages easier to view and edit.
2. Browsers do not normally use chunked encoding in requests, and it is normally seen only in server responses.

-As the HTTP/1 specification provides two different methods for specifying the length of HTTP messages, **it is possible for a single message to use both methods at once, such that they conflict with each other**. The specification attempts to prevent this problem by stating that **if both the Content-Length and Transfer-Encoding headers are present, then the Content-Length header should be ignored**. This might be sufficient **to avoid ambiguity when only a single server is in play**, but not when two or more servers are chained together. In this situation, problems can arise for two reasons:

1. Some servers do not support the Transfer-Encoding header in requests.
2. Some servers that do support the Transfer-Encoding header can be induced not to process it if the header is obfuscated in some way.
   If the front-end and back-end servers behave differently in relation to the (possibly obfuscated) Transfer-Encoding header, then they might disagree about the boundaries between successive requests, leading to request smuggling vulnerabilities.
   > Ambiguity in HTTP occurs when a request or message can be interpreted in multiple ways by different systems
   > How chunked encoding works (IMPORTANT)

Example HTTP request:

```http
POST /submit HTTP/1.1
Host: example.com
Transfer-Encoding: chunked

4
Wiki
5
pedia
0
```

What’s happening:

Each chunk =

<chunk-size in HEX>
<chunk-data>

So above:

4 → 4 bytes → Wiki

5 → 5 bytes → pedia

0 → end of body ✅

The server keeps reading chunks until it sees 0.

> Key rules you MUST remember ⚠️
> **1️⃣ Transfer-Encoding overrides Content-Length**

If both are present:

Transfer-Encoding: chunked
Content-Length: 100

➡️ Correct behavior:
Ignore Content-Length, use chunked.

(BUGGY servers don’t → security issues 👀)

**2️⃣ Chunk sizes are in hexadecimal**
A = 10 bytes

10 = 16 bytes

### 👉 HTTP Request Smuggling

This is why attackers love:

-Transfer-Encoding: chunked
Content-Length: 4

-Real-world uses (legit)

-Streaming responses

-Proxy → backend communication

-APIs sending unknown-sized data

-File uploads

> TL;DR

-Transfer-Encoding: chunked = body sent in pieces

-Each piece has a hex size

-Ends with 0

-Overrides Content-Length

-Parsing mismatches = 🔥 vulnerabilities

>Note
-**Websites that use HTTP/2 end-to-end are inherently immune to request smuggling attacks. As the HTTP/2 specification introduces a single, robust mechanism for specifying the length of a request, there is no way for an attacker to introduce the required ambiguity.**

-**However, many websites have an HTTP/2-speaking front-end server, but deploy this in front of back-end infrastructure that only supports HTTP/1. This means that the front-end effectively has to translate the requests it receives into HTTP/1. This process is known as HTTP downgrading. For more information**

>Note
-These techniques are only possible using HTTP/1 requests. Browsers and other clients, including Burp, use HTTP/2 by default to communicate with servers that explicitly advertise support for it during the TLS handshake.

-As a result, when testing sites with HTTP/2 support, you need to manually switch protocols in Burp Repeater. You can do this from the Request attributes section of the Inspector panel.

