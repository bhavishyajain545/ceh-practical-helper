# gobuster — directory / DNS / vhost brute-forcer

> **Fast Go-based fuzzer.** Use it whenever the question is "find the hidden page / subdomain / vhost". Pairs with [dirb](dirb.md) (slower, different wordlist) and [ffuf](ffuf.md) (more flexible).

**Install check (Parrot — already installed):** `gobuster version`

---

## 🎯 Cheat-flow: "What mode do I use?"

| You need to find... | Run this | Mode |
|---|---|---|
| Hidden directories / files | `gobuster dir -u http://<IP> -w <WORDLIST>` | `dir` |
| Subdomains of a domain | `gobuster dns -d <domain> -w <WORDLIST>` | `dns` |
| Virtual hosts (same IP) | `gobuster vhost -u http://<IP> -w <WORDLIST>` | `vhost` |
| Dirs with extensions | `gobuster dir -u http://<IP> -w <WORDLIST> -x php,html,txt` | `dir -x` |
| Behind login | `gobuster dir -u http://<IP> -w <WORDLIST> -c "PHPSESSID=abc"` | `dir -c` |
| Through a proxy | `gobuster dir -u http://<IP> -w <WORDLIST> --proxy http://127.0.0.1:8080` | any |

---

## 🔑 Flags you must know cold

### Common (all modes)
| Flag | Meaning |
|---|---|
| `-w <WORDLIST>` | Wordlist (required) |
| `-t 50` | Threads (default 10; 50 is safe) |
| `-o out.txt` | Output file |
| `-q` | Quiet (no banner) |
| `-z` | No progress |
| `-k` | Skip SSL verify |
| `--no-error` | Hide errors |

### `dir` mode
| Flag | Meaning |
|---|---|
| `-u <URL>` | Target URL |
| `-x php,html,txt,bak` | Extensions to append |
| `-s 200,204,301,302,307,401,403` | Status codes to show |
| `-b 404,500` | Blacklist status codes |
| `-c "name=val"` | Cookie (for auth) |
| `-H "Header: value"` | Custom header |
| `-U user -P pass` | HTTP basic auth |
| `-a "<UA>"` | User-agent |
| `-r` | Follow redirects |
| `-e` | Expanded — print full URL |
| `-f` | Append `/` to each word |

### `dns` mode
| Flag | Meaning |
|---|---|
| `-d <domain>` | Target domain |
| `-r 8.8.8.8` | Custom resolver |
| `-i` | Show IPs |
| `--wildcard` | Allow wildcard DNS |

### `vhost` mode
| Flag | Meaning |
|---|---|
| `-u <URL>` | Target URL |
| `--append-domain` | Append base domain to words |

---

## 📋 Wordlists (know these paths cold)

| Path | What |
|---|---|
| `/usr/share/wordlists/dirb/common.txt` | **Small, fast** — ~4600 words. First pass. |
| `/usr/share/wordlists/dirb/big.txt` | Bigger dirb list |
| `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` | **The classic medium list** (~220k) |
| `/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt` | Smaller dirbuster list |
| `/usr/share/seclists/Discovery/Web-Content/common.txt` | Seclists common |
| `/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt` | Raft — high quality |
| `/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt` | **DNS — first pass** |
| `/usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt` | DNS — deeper |

If `seclists` is missing: `sudo apt install seclists`.

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Fast first-pass dir scan
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -t 50

# 2. With common web extensions
gobuster dir -u http://<IP> \
  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -x php,html,txt,bak,zip -t 50 -o gob_dir.txt

# 3. Authenticated
gobuster dir -u http://<IP>/admin -w /usr/share/wordlists/dirb/common.txt \
  -c "PHPSESSID=abcd1234" -t 50

# 4. HTTPS with self-signed cert
gobuster dir -u https://<IP> -w /usr/share/wordlists/dirb/common.txt -k

# 5. Show only interesting statuses
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt \
  -s "200,204,301,302,307,401,403"

# 6. Subdomain enumeration
gobuster dns -d <domain> \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -t 50 -i -o gob_dns.txt

# 7. Virtual host enumeration
gobuster vhost -u http://<IP> \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -t 50 --append-domain -o gob_vhost.txt

# 8. Through Burp
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt \
  --proxy http://127.0.0.1:8080
```

---

## ⚠️ Gotchas

- **Wordlist not found?** Parrot ships `dirb` and `dirbuster` lists by default; `seclists` may need installing. `ls /usr/share/wordlists/`.
- **Getting 403 on everything?** The app may require a `Host:` header (use `vhost` mode) or a cookie.
- **`dns` mode returning nothing** → you may need `-r 8.8.8.8` or the wildcard flag.
- **Huge medium wordlist is slow** — start with `common.txt` first, escalate only if needed.
- **Extensions matter.** A PHP app with no `-x php` will miss every file. Always add `-x` based on the stack (check with [nikto](nikto.md) or nmap first).
- **Gobuster doesn't do recursion.** For recursive dir busting use [ffuf](ffuf.md) `-recursion`.
- **Status 301/302** often points to the real directory — follow with `-r` or investigate manually.
- **Answer format:** the exam usually wants the *path* exactly as gobuster printed it (e.g. `/admin.php`).

---

## 🔗 Related

- [dirb](dirb.md) — slower, different wordlist — run if gobuster misses
- [ffuf](ffuf.md) — more flexible fuzzer, supports recursion and parameters
- [nikto](nikto.md) — baseline web vuln scan
- [wpscan](wpscan.md) — WordPress-specific
- [sqlmap](sqlmap.md) — once you find an endpoint with params
- [05-web-app domain README](../05-web-app/README.md)
