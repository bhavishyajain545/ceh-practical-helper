# dirb — classic web content scanner

> **Old-school but still in the exam.** Slower than [gobuster](gobuster.md) / [ffuf](ffuf.md) but ships with its own wordlists and is dead simple. Run it as a second opinion when gobuster misses something.

**Install check (Parrot — already installed):** `dirb` (prints help)

---

## 🎯 Cheat-flow: "What do I run?"

| You need to... | Run this |
|---|---|
| Default scan (uses `common.txt`) | `dirb http://<IP>` |
| Custom wordlist | `dirb http://<IP> <WORDLIST>` |
| Specific extensions | `dirb http://<IP> -X .php,.html,.txt` |
| Behind cookie auth | `dirb http://<IP> -c "PHPSESSID=abc"` |
| HTTP basic auth | `dirb http://<IP> -u user:pass` |
| Save output | `dirb http://<IP> -o dirb.txt` |
| Non-recursive (fast) | `dirb http://<IP> -r` |
| Show only 200s | `dirb http://<IP> -N 404 -w` |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-a <UA>` | User-agent |
| `-c <cookie>` | Cookie string |
| `-H "Header: val"` | Extra header |
| `-u user:pass` | HTTP basic auth |
| `-p proxy:port` | Proxy |
| `-X .ext1,.ext2` | Extensions to append |
| `-x ext_file.txt` | Extensions from file |
| `-N <code>` | Ignore responses with status N |
| `-o <file>` | Save output |
| `-r` | **Non-recursive** (yes, `-r` means don't recurse — inverse of most tools) |
| `-R` | Interactive recursion (ask per dir) |
| `-S` | Silent mode |
| `-w` | Don't stop on warning messages |
| `-z <ms>` | Delay between requests (ms) |
| `-f` | Fine-tune NOT_FOUND detection |
| `-i` | Case-insensitive |

---

## 📋 Wordlists (dirb ships its own in `/usr/share/wordlists/dirb/`)

| File | Size | When |
|---|---|---|
| `common.txt` | ~4600 | **Default** — fast first pass |
| `small.txt` | ~960 | Super quick |
| `big.txt` | ~20k | Deeper |
| `extensions_common.txt` | — | Feed to `-x` |
| `vulns/apache.txt` | — | Apache-specific checks |
| `vulns/iis.txt` | — | IIS-specific |
| `vulns/tomcat.txt` | — | Tomcat paths |
| `others/best1050.txt` | ~1050 | Curated |
| `stress/alphanum_case.txt` | — | Bruteforce alphanum |

Full paths:
```
/usr/share/wordlists/dirb/common.txt
/usr/share/wordlists/dirb/big.txt
/usr/share/wordlists/dirb/vulns/apache.txt
```

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Default first pass
dirb http://<IP>

# 2. Bigger wordlist
dirb http://<IP> /usr/share/wordlists/dirb/big.txt

# 3. PHP app — append extensions
dirb http://<IP> /usr/share/wordlists/dirb/common.txt -X .php,.html,.txt,.bak

# 4. Authenticated scan
dirb http://<IP>/members -c "PHPSESSID=abcd1234"

# 5. Basic auth
dirb http://<IP> /usr/share/wordlists/dirb/common.txt -u admin:admin

# 6. Apache-specific vuln paths
dirb http://<IP> /usr/share/wordlists/dirb/vulns/apache.txt

# 7. Tomcat manager hunt
dirb http://<IP>:8080 /usr/share/wordlists/dirb/vulns/tomcat.txt

# 8. Save + ignore noise
dirb http://<IP> -o dirb.txt -N 403

# 9. Non-recursive quick scan
dirb http://<IP> -r -S

# 10. Through Burp
dirb http://<IP> -p 127.0.0.1:8080
```

---

## ⚠️ Gotchas

- **`-r` is backwards.** In dirb, `-r` means *don't* recurse (other tools use `-r` to enable recursion). Easy to trip on.
- **Slow.** Dirb is single-threaded and much slower than [gobuster](gobuster.md) or [ffuf](ffuf.md). Use it as a secondary scan.
- **Default wordlist is tiny** — if nothing interesting comes up, escalate to `big.txt` or seclists.
- **Extensions MUST start with a dot.** `-X .php` (correct), `-X php` (wrong).
- **Dirb follows redirects by default** and can run forever on some sites. Use `-S` + capture output.
- **Ignores 403 by default** — add `-N 403` only if you want to explicitly drop them (or remove for visibility).
- **HTTPS** — dirb auto-detects from `https://` URL prefix; no special flag needed.
- **Still a valid exam answer path** — the test sometimes specifically asks "using dirb, find...".

---

## 🔗 Related

- [gobuster](gobuster.md) — faster, better for big wordlists
- [ffuf](ffuf.md) — most flexible; supports recursion + filters
- [nikto](nikto.md) — run alongside for vuln findings
- [wpscan](wpscan.md) — WordPress
- [sqlmap](sqlmap.md) — after you find a param
- [05-web-app domain README](../12-web-apps/README.md)
