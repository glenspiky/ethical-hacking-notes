# 📌 Curl Cheat Sheet — Beginner to Hacker

## ✅ Day 8 — Curl Like a Hacker

### 1️⃣ Basics
- [ ] Simple GET request
```bash
curl https://example.com
```
- [ ] Pretty-print JSON (requires `jq`)
```bash
curl https://example.com/data | jq
```
- [ ] Include headers in output
```bash
curl -i https://example.com
```
- [ ] Only show headers
```bash
curl -I https://example.com
```

---

### 2️⃣ Custom Headers
- [ ] Add a custom header
```bash
curl -H "User-Agent: GlenHacker" https://example.com
```
- [ ] Content-Type header (JSON)
```bash
curl -H "Content-Type: application/json" https://example.com
```
- [ ] Send multiple headers
```bash
curl -H "Header1: value1" -H "Header2: value2" https://example.com
```

---

### 3️⃣ HTTP Methods
- [ ] GET (default)
```bash
curl https://example.com
```
- [ ] POST
```bash
curl -X POST https://example.com/api \
  -H "Content-Type: application/json" \
  -d '{"key":"value"}'
```
- [ ] PUT
```bash
curl -X PUT https://example.com/api/1 \
  -H "Content-Type: application/json" \
  -d '{"key":"updated"}'
```
- [ ] DELETE
```bash
curl -X DELETE https://example.com/api/1
```

---

### 4️⃣ Cookies
- [ ] Save cookies to a file
```bash
curl -c cookies.txt https://example.com
```
- [ ] Send cookies from a file
```bash
curl -b cookies.txt https://example.com
```
- [ ] Set cookie manually
```bash
curl -H "Cookie: session=abc123" https://example.com
```

---

### 5️⃣ Authentication
- [ ] Basic Auth
```bash
curl -u username:password https://example.com
```
- [ ] Bearer Token (JWT)
```bash
curl -H "Authorization: Bearer YOUR_TOKEN" https://example.com
```

---

### 6️⃣ Advanced / Hacker Mode
- [ ] Verbose mode (`-v`)
```bash
curl -v https://example.com
```
- [ ] Follow redirects (`-L`)
```bash
curl -L https://example.com
```
- [ ] Show headers only (`-I`)
```bash
curl -I https://example.com
```
- [ ] Combine `-I`, `-L`, `-v` (inspect headers, follow redirects, debug)
```bash
curl -ILv https://example.com
```
- [ ] Ignore SSL certificate errors (`-k`)
```bash
curl -k https://self-signed.badssl.com
```
- [ ] Send raw JSON from file
```bash
curl -X POST https://example.com/api \
  -H "Content-Type: application/json" \
  -d @data.json
```
- [ ] Download a file
```bash
curl -O https://example.com/file.zip
```
- [ ] Download silently
```bash
curl -s -O https://example.com/file.zip
```
- [ ] Limit download speed
```bash
curl --limit-rate 50K -O https://example.com/file.zip
```

---

### 7️⃣ Output Options
- [ ] Save response to file
```bash
curl https://example.com -o response.txt
```
- [ ] Append output
```bash
curl https://example.com >> response.txt
```
- [ ] Show only HTTP status code
```bash
curl -o /dev/null -s -w "%{http_code}\n" https://example.com
```

---

### 8️⃣ Misc Useful Flags
- [ ] Follow redirects: `-L`  
- [ ] Silent mode: `-s`  
- [ ] Include headers: `-i`  
- [ ] Show only headers: `-I`  
- [ ] Verbose / debug: `-v`  
- [ ] Ignore SSL cert errors: `-k`  
- [ ] Combine `-ILv` to debug + follow redirects + inspect headers  
- [ ] Data from file: `-d @file.json`  

---

### ✅ Practice Tips
1. Start with GET requests on public APIs (`jsonplaceholder.typicode.com`)  
2. Try sending custom headers & cookies  
3. Experiment with POST, PUT, DELETE  
4. Use `-v` to see the full request & response  
5. Combine flags: `curl -ILvk -H "User-Agent: Test"`

---

