# wpscan — WordPress security scanner

> **If the site is WordPress, wpscan is the answer.** Enumerates users, plugins, themes, versions, and brute-forces logins. Pair with [sqlmap](sqlmap.md) once you find a vulnerable plugin.

**Install check (Parrot — already installed):** `wpscan --version`

---

## 🎯 Cheat-flow: "What do I run?"

| You need to... | Run this |
|---|---|
| Basic scan | `wpscan --url http://<IP>` |
| **Enumerate users** | `wpscan --url http://<IP> -e u` |
| Enumerate plugins | `wpscan --url http://<IP> -e p` |
| Vulnerable plugins only | `wpscan --url http://<IP> -e vp` |
| Vulnerable themes only | `wpscan --url http://<IP> -e vt` |
| All the things | `wpscan --url http://<IP> -e ap,at,u,tt,cb,dbe` |
| **Brute-force a user** | `wpscan --url http://<IP> -U admin -P <WORDLIST>` |
| With API token (CVE data) | `wpscan --url http://<IP> --api-token <TOKEN>` |
| Behind self-signed cert | `wpscan --url https://<IP> --disable-tls-checks` |

---

## 🔑 Flags you must know cold

### Target / transport
| Flag | Meaning |
|---|---|
| `--url <URL>` | Target (required) |
| `--api-token <tok>` | wpvulndb.com / wpscan.com API token (free at wpscan.com) |
| `--disable-tls-checks` | Skip SSL verify |
| `--proxy http://127.0.0.1:8080` | Burp |
| `--cookie-string "a=b; c=d"` | Cookies |
| `--http-auth user:pass` | HTTP basic |
| `--user-agent "..."` | Custom UA |
| `--random-user-agent` | Randomize |
| `--throttle <ms>` | Delay between requests |
| `--request-timeout 60` | Per-request timeout |
| `-t 20` / `--max-threads 20` | Threads |

### Enumeration (`-e` / `--enumerate`)
| Code | Meaning |
|---|---|
| `u` | Users (default range 1-10) |
| `u1-100` | Users 1-100 |
| `p` | **All** plugins |
| `vp` | **Vulnerable** plugins only |
| `ap` | All plugins (most popular) |
| `t` | All themes |
| `vt` | Vulnerable themes |
| `at` | All themes (popular) |
| `tt` | Timthumbs |
| `cb` | Config backups |
| `dbe` | DB exports |
| `m` | Media |

### Detection depth
| Flag | Meaning |
|---|---|
| `--plugins-detection mixed\|passive\|aggressive` | How hard to probe plugins (aggressive = noisier, finds more) |
| `--plugins-version-detection ...` | Same for plugin versions |
| `--themes-detection ...` | Themes equivalent |

### Password attack
| Flag | Meaning |
|---|---|
| `-U <user>` / `--usernames <user>` | User (or file) |
| `-P <file>` / `--passwords <file>` | Password list |
| `--password-attack xmlrpc\|xmlrpc-multicall\|wp-login` | Attack vector |
| `--max-threads 50` | Threads |

### Output
| Flag | Meaning |
|---|---|
| `-o <file>` / `--output <file>` | Save to file |
| `-f cli\|json\|cli-no-color` | Format |
| `--no-banner` | Clean output |
| `-v` | Verbose |

---

## 📋 Wordlists (know these paths)

| Path | What |
|---|---|
| `/usr/share/wordlists/rockyou.txt` | **The big one** — gunzip first: `gunzip /usr/share/wordlists/rockyou.txt.gz` |
| `/usr/share/seclists/Passwords/Common-Credentials/10k-most-common.txt` | Top 10k |
| `/usr/share/seclists/Passwords/Common-Credentials/best1050.txt` | Small curated list |
| `/usr/share/wordlists/dirb/others/best110.txt` | Tiny but fast |

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Default scan
wpscan --url http://<IP>

# 2. Enumerate users (most common exam Q)
wpscan --url http://<IP> -e u --no-banner

# 3. Find vulnerable plugins (needs API token for CVE data)
wpscan --url http://<IP> -e vp --api-token <TOKEN>

# 4. Aggressive plugin enumeration (slower but thorough)
wpscan --url http://<IP> -e ap --plugins-detection aggressive \
  --api-token <TOKEN>

# 5. Enumerate everything
wpscan --url http://<IP> -e ap,at,u,tt,cb,dbe --api-token <TOKEN>

# 6. Brute-force admin via xmlrpc.php (fast)
wpscan --url http://<IP> -U admin \
  -P /usr/share/wordlists/rockyou.txt \
  --password-attack xmlrpc --max-threads 50

# 7. Brute-force via wp-login.php (when xmlrpc disabled)
wpscan --url http://<IP> -U admin \
  -P /usr/share/wordlists/rockyou.txt \
  --password-attack wp-login

# 8. Multi-user brute (from enumeration)
wpscan --url http://<IP> -e u
# copy usernames into users.txt, then:
wpscan --url http://<IP> -U users.txt \
  -P /usr/share/wordlists/rockyou.txt \
  --password-attack xmlrpc-multicall

# 9. Save to JSON for parsing
wpscan --url http://<IP> -e vp,vt,u -f json -o wpscan.json \
  --api-token <TOKEN>

# 10. Through Burp
wpscan --url http://<IP> --proxy http://127.0.0.1:8080 \
  --disable-tls-checks
```

---

## ⚠️ Gotchas

- **API token is free but required** for CVE / vulnerability data. Without it wpscan lists plugins/versions but not "known vulnerable". Grab one at wpscan.com (25 req/day free tier). In the exam, check if a token is already configured in `~/.wpscan/scan.yml`.
- **User enum** uses `?author=N` by default — works on most sites. If blocked, try `--enumerate u1-50` with aggressive detection.
- **xmlrpc brute is 10x faster** than wp-login brute (batch multicall). Always try `--password-attack xmlrpc-multicall` first; fall back to `wp-login` if xmlrpc is disabled (common on hardened sites).
- **Rate limits / WAF?** Drop `--max-threads` to 5 and add `--throttle 500`.
- **Passive detection misses plugins** — if default scan shows zero plugins on an obvious WP site, rerun with `--plugins-detection aggressive`.
- **HTTPS self-signed** → `--disable-tls-checks`.
- **"not WordPress"** error — try `--force` to override detection.
- **The exam often chains wpscan → metasploit / sqlmap** — once you find a vulnerable plugin, search exploit-db or metasploit for it.
- **Answer format** — usernames from `-e u` come back as a list; the exam usually wants the first/admin account exactly as printed.

---

## 🔗 Related

- [sqlmap](sqlmap.md) — chase vulnerable-plugin SQLi after wpscan finds one
- [nikto](nikto.md) — baseline that also detects WordPress
- [gobuster](gobuster.md) — find `/wp-admin/`, `/wp-content/` if wpscan can't detect
- [ffuf](ffuf.md) — fuzz plugin paths manually
- [nmap](nmap.md) — `http-wordpress-*` NSE scripts
- [05-web-app domain README](../05-web-app/README.md)
