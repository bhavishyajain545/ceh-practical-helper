# nikto — web server vulnerability scanner

> **Quick baseline web scan.** Loud, noisy, but surfaces outdated software, dangerous files, config mistakes. Run it early alongside [gobuster](gobuster.md) / [nmap](nmap.md) HTTP scripts.

**Install check (Parrot — already installed):** `nikto -Version`

---

## 🎯 Cheat-flow: "What do I run?"

| You need to... | Run this |
|---|---|
| Basic scan | `nikto -h http://<IP>` |
| Scan with port | `nikto -h <IP> -p 8080` |
| HTTPS | `nikto -h https://<IP> -ssl` |
| Multiple ports | `nikto -h <IP> -p 80,443,8080` |
| Save output | `nikto -h http://<IP> -o nikto.txt -Format txt` |
| Behind auth | `nikto -h http://<IP> -id "user:pass"` |
| Through Burp | `nikto -h http://<IP> -useproxy http://127.0.0.1:8080` |
| Tune to one check | `nikto -h http://<IP> -Tuning <n>` |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-h <host>` | Target (URL or IP, required) |
| `-p <port>` | Port (or comma-list / range) |
| `-ssl` | Force SSL |
| `-nossl` | Force plain HTTP |
| `-root /app` | Prepend path to all requests |
| `-id user:pass` | HTTP basic auth |
| `-useragent "..."` | Custom UA |
| `-useproxy <url>` | Proxy through Burp |
| `-o <file>` | Output file |
| `-Format txt\|html\|csv\|xml\|json` | Output format (must match `-o` extension) |
| `-Display V` | Verbose |
| `-Display D` | Debug |
| `-evasion <n>` | IDS evasion (1-8) |
| `-Tuning <n>` | Limit checks to category |
| `-Plugins <name>` | Run specific plugin |
| `-list-plugins` | Show plugins |
| `-update` | Update DB |
| `-timeout 10` | Per-request timeout |
| `-maxtime 5m` | Total scan cap |
| `-ask no` | Don't prompt |

### `-Tuning` categories (useful in exam)
| N | Category |
|---|---|
| `0` | File upload |
| `1` | Interesting files / seen in logs |
| `2` | Misconfiguration / default files |
| `3` | Information disclosure |
| `4` | Injection (XSS/Script/HTML) |
| `5` | Remote file retrieval (inside web root) |
| `6` | Denial of service |
| `7` | Remote file retrieval (server-wide) |
| `8` | Command execution / RCE |
| `9` | SQL injection |
| `a` | Auth bypass |
| `b` | Software identification |
| `c` | Remote source inclusion |
| `x` | Reverse tuning (exclude) |

---

## 🧪 What nikto finds (common exam answers)

- **Server version** — Apache/IIS/nginx exact version string
- **Outdated server** — "Apache/2.2.8 appears to be outdated"
- **Dangerous HTTP methods** — PUT, DELETE, TRACE enabled
- **Default files** — `/phpinfo.php`, `/server-status`, `/test.php`
- **Directory indexing** enabled
- **Admin panels** — `/admin/`, `/manager/html` (Tomcat!)
- **Missing headers** — X-Frame-Options, X-XSS-Protection
- **Robots.txt** entries (free recon)
- **ShellShock / Heartbleed** on old boxes
- **CGI dirs** — `/cgi-bin/`
- **Tomcat default creds** (`tomcat:tomcat`, `admin:admin`)

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Baseline scan
nikto -h http://<IP>

# 2. Save in multiple formats
nikto -h http://<IP> -o nikto.txt -Format txt
nikto -h http://<IP> -o nikto.html -Format html

# 3. Non-standard port + HTTPS
nikto -h https://<IP>:8443 -ssl

# 4. Scan an app subfolder
nikto -h http://<IP> -root /app/

# 5. Only look for injection + RCE
nikto -h http://<IP> -Tuning 489

# 6. Through Burp (debug what it's doing)
nikto -h http://<IP> -useproxy http://127.0.0.1:8080

# 7. Authenticated
nikto -h http://<IP> -id "admin:password"

# 8. Cap runtime (exam clock!)
nikto -h http://<IP> -maxtime 5m -ask no

# 9. Multi-port one shot
nikto -h <IP> -p 80,443,8080,8443
```

---

## ⚠️ Gotchas

- **Nikto is LOUD** — generates thousands of requests, obvious in logs. Fine for exam, not real engagements.
- **False positives are common.** If nikto flags "XSS in /index.php", verify manually before submitting it as an answer.
- **"OSVDB-xxxx" references are dead** — OSVDB shut down. Use the text description instead.
- **`-Format` must match `-o` extension** or nikto errors out.
- **Slow on big targets** — use `-maxtime 5m` in exam conditions.
- **Doesn't follow auth flows** — if the app redirects to a login, use `-id` or feed it a cookie via `-Plugins "cookie"`.
- **Run BEFORE heavy dir busting.** Nikto's output tells you which extensions and CMS to target with [gobuster](gobuster.md) / [wpscan](wpscan.md).
- **Answer format** — exam often wants the *exact* server string (e.g. `Apache/2.4.29 (Ubuntu)`). Copy it verbatim.

---

## 🔗 Related

- [nmap](nmap.md) — `--script http-*` overlaps with nikto
- [gobuster](gobuster.md) — directory brute-force after nikto finds the stack
- [dirb](dirb.md) — slower but different wordlist
- [wpscan](wpscan.md) — if nikto sees WordPress
- [sqlmap](sqlmap.md) — once you have a param
- [05-web-app domain README](../05-web-app/README.md)
