# searchsploit — local exploit-db search

> **Instant offline exploit lookup.** When nmap `-sV` spits out a version, searchsploit tells you if there's a public exploit.

**Install check:** `searchsploit -h`

---

## 🎯 Cheat-flow

| Task | Command |
|---|---|
| Keyword search | `searchsploit vsftpd 2.3.4` |
| Title-only match | `searchsploit -t apache` |
| Exact match | `searchsploit -e "Apache 2.4.49"` |
| **Copy exploit locally** | `searchsploit -m 49757` |
| View exploit source | `searchsploit -x 49757` |
| Exclude noise terms | `searchsploit apache -excludepath "/dos/"` |
| Search by CVE | `searchsploit --cve 2021-41773` |
| JSON output | `searchsploit -j apache` |
| Update DB | `searchsploit -u` |
| Case-sensitive | `searchsploit -c joomla` |
| Nmap XML → auto-look | `searchsploit --nmap scan.xml` |

---

## 🔑 Key flags

| Flag | Meaning |
|---|---|
| `-t` | Title only (less noise) |
| `-e` | Exact match |
| `-s` | Strict (filter versions) |
| `-m <id>` | Mirror (copy) exploit into current dir |
| `-x <id>` | Examine (open in pager) |
| `-p <id>` | Show path/info |
| `-j` | JSON output |
| `-u` | Update the repo |
| `-c` | Case sensitive |
| `-w` | Show exploit-db.com URL |
| `--cve <id>` | Filter by CVE |
| `--nmap <xml>` | Parse nmap XML and auto-search |
| `--exclude "term"` | Exclude results matching term |

---

## 📋 Recipes

```bash
# 1. Check a specific service version
searchsploit vsftpd 2.3.4
# → unix/remote/17491.rb  (Backdoor Command Execution)

# 2. Pull a copy into current directory
searchsploit -m 17491
# copies to ./17491.rb

# 3. Skim the source without copying
searchsploit -x 17491

# 4. Feed nmap XML directly
nmap -sV -oX scan.xml 10.10.10.5
searchsploit --nmap scan.xml

# 5. CVE-based
searchsploit --cve 2014-6271   # shellshock

# 6. Strict version match
searchsploit -s "apache 2.4.49"
```

---

## ⚠️ Gotchas

- Exploit DB path: `/usr/share/exploitdb/exploits/` (source) and `.../shellcodes/`.
- Update before exam day: `searchsploit -u`.
- Most exploits **need editing** — LHOST, LPORT, URL, padding. Open with `-x`.
- Searches OR by default — results can be huge. Tighten with `-t`.
- `-s` filters numeric versions but only on **title** — double-check manually.
- Version matching is dumb-string — "Apache/2.4" matches "Apache/2.4.49" *and* "Apache/2.4.1".

---

## 🔗 Related

- [metasploit](metasploit.md) · [nmap](nmap.md) · [msfvenom](msfvenom.md)
