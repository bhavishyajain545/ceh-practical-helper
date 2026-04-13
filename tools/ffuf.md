# ffuf — fast web fuzzer

> **The Swiss-army knife of web fuzzing.** Faster than [gobuster](gobuster.md), more flexible than [dirb](dirb.md). Anywhere you can put `FUZZ`, ffuf will fuzz it — URLs, headers, bodies, cookies, vhosts, parameters.

**Install check (Parrot — already installed):** `ffuf -V`

---

## 🎯 Cheat-flow: "Where do I put FUZZ?"

| You need to find... | Run this |
|---|---|
| Hidden directories | `ffuf -u http://<IP>/FUZZ -w <WORDLIST>` |
| Files with extensions | `ffuf -u http://<IP>/FUZZ -w <WORDLIST> -e .php,.html,.txt` |
| Subdomains (DNS) | `ffuf -u http://FUZZ.<domain> -w <WORDLIST>` |
| **Virtual hosts** | `ffuf -u http://<IP> -H "Host: FUZZ.<domain>" -w <WORDLIST> -fs <size>` |
| GET parameters | `ffuf -u "http://<IP>/page.php?FUZZ=test" -w <WORDLIST> -fs <size>` |
| Parameter values | `ffuf -u "http://<IP>/page.php?id=FUZZ" -w <WORDLIST>` |
| POST body fuzz | `ffuf -u http://<IP>/login -X POST -d "user=admin&pass=FUZZ" -w <WORDLIST>` |
| Header fuzz | `ffuf -u http://<IP> -H "X-Api-Key: FUZZ" -w <WORDLIST>` |
| Recursive dir bust | `ffuf -u http://<IP>/FUZZ -w <WORDLIST> -recursion -recursion-depth 2` |

---

## 🔑 Flags you must know cold

### Core
| Flag | Meaning |
|---|---|
| `-u <URL>` | Target URL (must contain `FUZZ` — or be used with `-w kw:FUZZ`) |
| `-w <file>` | Wordlist — multiple allowed with keywords: `-w users.txt:USER -w pass.txt:PASS` |
| `-t 40` | Threads (default 40) |
| `-p 0.1-1.0` | Delay range between requests |
| `-timeout 10` | Request timeout |
| `-rate 100` | Cap requests per second |
| `-H "Header: val"` | Custom header (repeatable) |
| `-X POST` | HTTP method |
| `-d "body"` | POST body |
| `-b "cookie=val"` | Cookies |
| `-r` | Follow redirects |
| `-x http://127.0.0.1:8080` | Proxy |
| `-k` | Ignore TLS errors |

### Matchers (what to SHOW)
| Flag | Meaning |
|---|---|
| `-mc 200,204,301,302,307,401,403` | Match status codes (default: 200-299,301,302,307,401,403,405,500) |
| `-ml <n>` | Match line count |
| `-mw <n>` | Match word count |
| `-ms <n>` | Match response size |
| `-mr <regex>` | Match regex in body |
| `-mc all` | Match all status codes (combine with filters) |

### Filters (what to HIDE — usually more useful)
| Flag | Meaning |
|---|---|
| `-fc 404,500` | Filter status codes |
| `-fl <n>` | Filter by line count |
| `-fw <n>` | Filter by word count |
| `-fs <n>` | **Filter by response size** (critical for vhost / param fuzzing) |
| `-fr <regex>` | Filter regex |

### Recursion & output
| Flag | Meaning |
|---|---|
| `-recursion` | Recurse into found dirs |
| `-recursion-depth 2` | Cap depth |
| `-recursion-strategy default\|greedy` | greedy = recurse on any match |
| `-e .php,.html,.txt,.bak` | Extensions |
| `-o out.json` | Save output |
| `-of json\|html\|csv\|md\|ejson\|all` | Output format |
| `-s` | Silent (just matches) |
| `-v` | Verbose (full URLs) |
| `-c` | Colorize |
| `-ac` | **Auto-calibrate** — auto-detect & filter wildcard responses |
| `-acc <string>` | Auto-calibrate with custom strings |

---

## 🧪 The filter-size trick (the most important ffuf skill)

When fuzzing vhosts, parameters, or values, the server often returns a default "not found" page with `200 OK` — defeating status filtering. The fix is to filter by **response size**:

```bash
# Step 1: run once, note the size of the noise response
ffuf -u "http://<IP>/?FUZZ=test" -w /usr/share/wordlists/dirb/common.txt
# output shows thousands of 200s all at size 1234

# Step 2: filter that size out
ffuf -u "http://<IP>/?FUZZ=test" -w /usr/share/wordlists/dirb/common.txt -fs 1234
```

Or use `-ac` to have ffuf figure it out automatically.

---

## 📋 Wordlists

| Path | What |
|---|---|
| `/usr/share/wordlists/dirb/common.txt` | Fast first-pass |
| `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` | Medium dir list |
| `/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt` | High-quality dirs |
| `/usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt` | Files |
| `/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt` | Subdomains |
| `/usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt` | **Parameter names** |

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Directory brute
ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirb/common.txt

# 2. With extensions + recursion
ffuf -u http://<IP>/FUZZ \
  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -e .php,.html,.txt,.bak -recursion -recursion-depth 2 \
  -o ffuf_dir.json -of json

# 3. Hide 403 noise, auto-calibrate wildcards
ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirb/common.txt -fc 403 -ac

# 4. Virtual host discovery
ffuf -u http://<IP> -H "Host: FUZZ.<domain>" \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -fs <baseline-size>

# 5. Subdomain (DNS via HTTP)
ffuf -u http://FUZZ.<domain> \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# 6. Parameter name discovery
ffuf -u "http://<IP>/page.php?FUZZ=value" \
  -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
  -fs <baseline-size>

# 7. Parameter value fuzz (brute login)
ffuf -u http://<IP>/login.php \
  -X POST -d "username=admin&password=FUZZ" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -w /usr/share/wordlists/rockyou.txt -fc 401,403 -fs <fail-size>

# 8. Multi-wordlist pitchfork (user+pass pairs)
ffuf -u http://<IP>/login -X POST \
  -d "user=USER&pass=PASS" \
  -w users.txt:USER -w passwords.txt:PASS \
  -mode pitchfork -fc 401

# 9. Cluster-bomb (every user × every pass)
ffuf -u http://<IP>/login -X POST -d "user=USER&pass=PASS" \
  -w users.txt:USER -w passwords.txt:PASS -mode clusterbomb -fc 401

# 10. Through Burp
ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirb/common.txt \
  -x http://127.0.0.1:8080 -k
```

---

## ⚠️ Gotchas

- **Forgot the `FUZZ` keyword** → ffuf exits immediately. Must appear in URL, header, or body.
- **Everything shows 200** → use `-fs` to filter by size, or add `-ac` to auto-calibrate.
- **Results scroll off-screen** → always save with `-o out.json -of json`. The `html` format is nice for browsing during the exam.
- **Rate limiting / WAF** → `-rate 20 -p 0.1-0.5` slows things down.
- **Recursion explodes** → cap with `-recursion-depth 2`. Without the cap ffuf will dig forever on sites with wildcards.
- **Wordlist order matters for pitchfork mode** — lines align index-to-index. For all combos use `-mode clusterbomb`.
- **`-e` extensions** must start with `.` (e.g. `.php`, not `php`).
- **Default filter hides 404** already, but other "error" pages may come back as 200 — verify with one manual request.
- **Answer format** — exam usually wants the path or parameter exactly (e.g. `/secret.php` or `debug`). Copy from ffuf's output column.

---

## 🔗 Related

- [gobuster](gobuster.md) — simpler, similar speed, no recursion
- [dirb](dirb.md) — slower fallback with different wordlists
- [nikto](nikto.md) — vuln scan before/after fuzzing
- [wpscan](wpscan.md) — WordPress-specific
- [sqlmap](sqlmap.md) — once ffuf finds the param
- [05-web-app domain README](../12-web-apps/README.md)
