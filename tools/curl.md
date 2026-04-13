# curl — HTTP swiss-army knife

> **Every web/API question can be answered with curl.** Faster than opening Burp for one-off requests.

**Install check:** `curl --version`

---

## 🎯 Cheat-flow

| Task | Command |
|---|---|
| Simple GET | `curl http://target/` |
| Follow redirects | `curl -L http://target/` |
| Ignore TLS errors | `curl -k https://target/` |
| Save body to file | `curl -o out.html http://target/` |
| Save *with* original filename | `curl -O http://target/file.zip` |
| Headers only | `curl -I http://target/` |
| Verbose (see request + response headers) | `curl -v http://target/` |
| POST form data | `curl -d "user=admin&pass=123" http://target/login` |
| POST JSON | `curl -H "Content-Type: application/json" -d '{"k":"v"}' http://target/api` |
| Set custom header | `curl -H "X-Forwarded-For: 127.0.0.1" http://target/` |
| Set cookie | `curl -b "session=abc123" http://target/` |
| Save cookies from response | `curl -c cookies.txt http://target/` |
| Replay with saved cookies | `curl -b cookies.txt http://target/` |
| Basic auth | `curl -u admin:admin http://target/` |
| Change HTTP method | `curl -X PUT http://target/api/1 -d 'data'` |
| Upload file | `curl -F "file=@shell.php" http://target/upload` |
| Proxy through Burp | `curl -x http://127.0.0.1:8080 -k https://target/` |
| Override DNS | `curl --resolve target.com:443:10.10.10.5 https://target.com/` |

---

## 🔑 Key flags

| Flag | Meaning |
|---|---|
| `-X METHOD` | HTTP verb (GET, POST, PUT, DELETE, PATCH, OPTIONS) |
| `-H "Name: val"` | Add header (repeat for multiple) |
| `-d "k=v"` | POST body (URL-encoded form by default) |
| `--data-binary @file` | POST raw file bytes (no newline munging) |
| `--data-urlencode "q=a b"` | URL-encode the value |
| `-G` | Convert `-d` into GET query string |
| `-b "k=v"` or `-b file` | Send cookies |
| `-c file` | Write received cookies to file (jar) |
| `-u user:pass` | HTTP basic auth |
| `-k` / `--insecure` | Skip cert verification |
| `-L` | Follow redirects |
| `-I` | HEAD request (headers only) |
| `-s` | Silent (no progress bar) |
| `-S` | Show errors even with `-s` |
| `-v` / `-vv` | Verbose / more verbose |
| `-o file` | Write body to file |
| `-O` | Save with remote name |
| `-A "UA string"` | User-Agent |
| `-e "referer"` | Referer header |
| `-x proxy:port` | Use proxy |
| `--resolve host:port:IP` | Force DNS answer (vhost testing) |
| `-w "%{http_code}"` | Print response info after |
| `--max-time 10` | Hard timeout |
| `-4` / `-6` | Force IPv4/IPv6 |

---

## 📋 Recipes

```bash
# 1. Grab just the HTTP status code
curl -s -o /dev/null -w "%{http_code}\n" http://target/

# 2. Virtual-host scan (Host header trick)
curl -s -H "Host: admin.target.com" http://10.10.10.5/

# 3. Test SQLi quickly
curl "http://target/item?id=1'"

# 4. Download via SOCKS proxy (chisel/ssh)
curl --socks5 127.0.0.1:1080 http://internal/

# 5. Login, save cookie, access dashboard
curl -c jar.txt -d "user=admin&pass=admin" http://target/login
curl -b jar.txt http://target/dashboard

# 6. Brute force one-liner (use hydra for real brute)
for p in $(cat pw.txt); do
  echo -n "$p: "
  curl -s -o /dev/null -w "%{http_code}\n" -u "admin:$p" http://target/
done

# 7. SSRF test
curl "http://target/fetch?url=http://127.0.0.1:22"

# 8. Verb tampering bypass
curl -X POST http://target/admin   # when GET is blocked

# 9. Large file upload via PUT
curl -T shell.jsp http://target/uploads/shell.jsp

# 10. Show only response headers + status
curl -sv http://target/ 2>&1 | grep -E "^[<>] "
```

---

## ⚠️ Gotchas

- `-d` auto-sets `Content-Type: application/x-www-form-urlencoded`. For JSON, add `-H "Content-Type: application/json"`.
- `-d @file` reads body from file but **strips newlines** — use `--data-binary @file` for binary/JSON-with-newlines.
- `-L` drops auth headers on cross-host redirect unless `--location-trusted`.
- `-I` sends HEAD — some servers answer differently than GET.
- Redirect to HTTPS + self-signed cert? `-kL`.
- `-v` sends to stderr — pipe with `2>&1`.

---

## 🔗 Related

- [burpsuite](burpsuite.md) · [ffuf](ffuf.md) · [wfuzz] · [wget] · [openssl](openssl.md) · [nuclei](nuclei.md)
