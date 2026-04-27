# HASH MILA — KAUNSA TYPE HAI? Pehchan Aur Crack Guide

---

## Hashing Kya Hai? (Basic Samjho)
Hashing ek **one-way function** hai — input se fixed-length output nikalta hai, lekin output
se wapas input nahi nikal sakta. Jaise meat grinder — chicken daalo toh keema nikle, lekin
keema se wapas chicken nahi bana sakta.

**Key Points:**
- Same input = ALWAYS same hash (deterministic)
- Thoda sa change = poora hash badal jaata hai (avalanche effect)
- Hash se password nahi nikal sakta — bas GUESS karke match karte hain (brute force/dictionary)
- **Hashing != Encryption** — Encryption reversible hai (key se), hashing nahi

**Toh hash kaise crack hota hai?**
Wordlist ke har word ka hash banao → target hash se compare karo → match mila = password!

---

## IDENTIFICATION BY LENGTH — Sabse Easy Method

Jab hash mile, pehle characters gin lo:

| Length | Hex Characters | Most Likely Type | Hashcat Mode | John Format |
|--------|---------------|-------------------|-------------|-------------|
| **32** | `0-9, a-f` | **MD5** | `-m 0` | `raw-md5` |
| **32** | `0-9, a-f` (Windows context) | **NTLM** | `-m 1000` | `nt` |
| **40** | `0-9, a-f` | **SHA-1** | `-m 100` | `raw-sha1` |
| **40** | `*` + hex | **MySQL 4.1+** | `-m 300` | `mysql-sha1` |
| **56** | `0-9, a-f` | **SHA-224** | `-m 1300` | `raw-sha224` |
| **64** | `0-9, a-f` | **SHA-256** | `-m 1400` | `raw-sha256` |
| **96** | `0-9, a-f` | **SHA-384** | `-m 10800` | `raw-sha384` |
| **128** | `0-9, a-f` | **SHA-512** | `-m 1700` | `raw-sha512` |

### Example:
```
5d41402abc4b2a76b9719d911017c592          ← 32 chars = MD5
aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d  ← 40 chars = SHA-1
e3b0c44298fc1c149afbf4c8996fb924...       ← 64 chars = SHA-256
```

### CONFUSING CASE: MD5 vs NTLM
Dono 32 character hex hain! Kaise pata chalega?
- **Windows SAM dump se mila** → NTLM (hashcat -m 1000)
- **Web app / database se mila** → MD5 (hashcat -m 0)
- **Linux /etc/shadow se mila** → MD5 nahi hai, ye MD5-crypt hoga ($1$ prefix dekho)
- **hashdump command se mila** → NTLM

---

## IDENTIFICATION BY PREFIX — Sabse Reliable Method

Kuch hashes ke starting mein special prefix hota hai — ye instantly type bata deta hai:

| Prefix | Type | Where Found | Hashcat | John |
|--------|------|-------------|---------|------|
| `$1$` | MD5-crypt | Linux /etc/shadow (old) | `-m 500` | `md5crypt` |
| `$2a$` / `$2b$` / `$2y$` | bcrypt | Web apps, modern Linux | `-m 3200` | `bcrypt` |
| `$5$` | SHA-256-crypt | Linux /etc/shadow | `-m 7400` | `sha256crypt` |
| `$6$` | SHA-512-crypt | Linux /etc/shadow (common) | `-m 1800` | `sha512crypt` |
| `$apr1$` | Apache MD5 | .htpasswd files | `-m 1600` | - |
| `$P$` / `$H$` | phpass | WordPress, Joomla | `-m 400` | `phpass` |
| `$krb5asrep$23$` | AS-REP Roast | Kerberos attack | `-m 18200` | `krb5asrep` |
| `$krb5tgs$23$` | Kerberoast | Kerberos attack | `-m 13100` | `krb5tgs` |
| `$argon2i$` / `$argon2id$` | Argon2 | Modern password storage | `-m 32600` | `argon2` |
| `{SHA}` / `{SSHA}` | LDAP SHA | LDAP directories | `-m 101` | `nsldap` |

### Examples:
```
$6$rounds=5000$salt$hash...        ← $6$ = SHA-512-crypt (Linux shadow)
$2a$10$N9qo8uLOick...              ← $2a$ = bcrypt (web app)
$P$B2VGK.FHkSPJPm/Yx...           ← $P$ = phpass (WordPress)
$krb5asrep$23$svc_sql@DOMAIN:...   ← AS-REP Roast hash
$krb5tgs$23$*svc_sql$DOMAIN$...    ← Kerberoast TGS hash
```

---

## IDENTIFICATION BY CONTEXT — Kahan Se Mila?

| Kahan Mila | Most Likely Type | Hashcat Mode | John Format |
|---|---|---|---|
| Linux `/etc/shadow` (`$6$...`) | SHA-512-crypt | `-m 1800` | `sha512crypt` |
| Linux `/etc/shadow` (`$1$...`) | MD5-crypt | `-m 500` | `md5crypt` |
| Windows `hashdump` output | NTLM | `-m 1000` | `nt` |
| Windows SAM file | NTLM | `-m 1000` | `nt` |
| MySQL `mysql.user` table | MySQL SHA1 (`*` + 40 hex) | `-m 300` | `mysql-sha1` |
| Web app database (32 hex) | MD5 (ya MD5 + salt) | `-m 0` | `raw-md5` |
| WordPress `wp_users` table | phpass (`$P$...`) | `-m 400` | `phpass` |
| .htpasswd file | Apache MD5 (`$apr1$...`) | `-m 1600` | - |
| WiFi handshake (.cap file) | WPA/WPA2 | `-m 22000` | - |
| Active Directory AS-REP | Kerberos AS-REP | `-m 18200` | `krb5asrep` |
| Active Directory Kerberoast | Kerberos TGS | `-m 13100` | `krb5tgs` |
| Mimikatz/kiwi output | NTLM | `-m 1000` | `nt` |

---

## AUTO-IDENTIFICATION TOOLS

### hashid (Best for Quick ID):
```bash
hashid 'PASTE_YOUR_HASH_HERE'
# Output:
# [+] MD5
# [+] NTLM
# ...possible types list

# Hashcat mode bhi batao:
hashid 'PASTE_HASH' -m
# [+] MD5 [Hashcat Mode: 0]
# [+] NTLM [Hashcat Mode: 1000]
```

### hash-identifier (Interactive):
```bash
hash-identifier
# Prompt aayega — hash paste karo, type batayega
```

### Name-That-Hash (Advanced):
```bash
nth --text 'PASTE_HASH'
# Most likely type pehle dikhata hai, confidence level ke saath
```

**WARNING:** Auto-tools multiple possibilities dete hain — CONTEXT use karo final decision ke liye!
Windows se mila = NTLM, Linux shadow se mila = crypt variant, web se mila = MD5/bcrypt

---

## CRACKING — STEP BY STEP

### Step 1: Hash File Banao
```bash
echo 'e02bc503339d51f71d28f7a21e2e9a0f' > hash.txt
# Ya multiple hashes ek file mein (ek per line)
```

### Step 2: John The Ripper Se Crack
```bash
# Basic (auto-detect format):
john hash.txt

# Specific format:
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --format=sha512crypt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# Result dekho:
john --show hash.txt
john --show --format=raw-md5 hash.txt
```

### Step 3: Hashcat Se Crack (Faster — GPU Use Karta Hai)
```bash
# Basic:
hashcat -m MODE hash.txt /usr/share/wordlists/rockyou.txt

# Examples:
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt       # MD5
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt    # NTLM
hashcat -m 1800 hash.txt /usr/share/wordlists/rockyou.txt    # SHA-512-crypt
hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt   # AS-REP
hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt   # Kerberoast

# Result dekho:
hashcat -m MODE hash.txt --show
```

### Step 4: Online Tools (Agar Local Crack Slow Hai)
```
https://crackstation.net        ← MD5, SHA1, NTLM — paste karo, result milega
https://hashes.com              ← Large database
```

---

## QUICK REFERENCE TABLE — Sab Ek Jagah

| Type | Length | Prefix | Hashcat | John | Example Start |
|------|--------|--------|---------|------|---------------|
| MD5 | 32 | - | 0 | raw-md5 | `5d41402a...` |
| NTLM | 32 | - | 1000 | nt | `e02bc503...` |
| SHA-1 | 40 | - | 100 | raw-sha1 | `aaf4c61d...` |
| SHA-256 | 64 | - | 1400 | raw-sha256 | `e3b0c442...` |
| SHA-512 | 128 | - | 1700 | raw-sha512 | `cf83e135...` |
| MD5-crypt | varies | `$1$` | 500 | md5crypt | `$1$salt$...` |
| SHA-512-crypt | varies | `$6$` | 1800 | sha512crypt | `$6$salt$...` |
| bcrypt | 60 | `$2a$`/`$2b$` | 3200 | bcrypt | `$2a$10$...` |
| phpass | 34 | `$P$`/`$H$` | 400 | phpass | `$P$B2VG...` |
| MySQL 4.1+ | 41 | `*` | 300 | mysql-sha1 | `*6C8989...` |
| AS-REP | long | `$krb5asrep$` | 18200 | krb5asrep | `$krb5asrep$23$...` |
| Kerberoast | long | `$krb5tgs$` | 13100 | krb5tgs | `$krb5tgs$23$...` |

---

## Common Mistakes (Mat Karna Ye)

1. **MD5 aur NTLM confuse karna** — Dono 32 hex hain, CONTEXT se pata lagao
2. **Hashcat mode galat daalna** — `-m 0` (MD5) aur `-m 1000` (NTLM) bahut different hain
3. **John mein `--format` miss karna** — Auto-detect galat guess kar sakta hai
4. **Hash file mein extra spaces/newline** — Clean file banao
5. **rockyou.txt compressed hona** — `sudo gunzip /usr/share/wordlists/rockyou.txt.gz` pehle
6. **$6$ hash ko SHA-512 samajhna** — `$6$` = SHA-512-**crypt** (salted), raw SHA-512 nahi!
7. **Online tools pe AD hashes daalna** — Sensitive hashes leak ho sakte hain

---

## Quick Decision Tree

```
Hash mila
  |
  +-- Prefix hai?
  |     +-- $6$ --> SHA-512-crypt (hashcat -m 1800)
  |     +-- $1$ --> MD5-crypt (hashcat -m 500)
  |     +-- $2a$/$2b$ --> bcrypt (hashcat -m 3200)
  |     +-- $P$/$H$ --> phpass/WordPress (hashcat -m 400)
  |     +-- $krb5asrep$ --> AS-REP (hashcat -m 18200)
  |     +-- $krb5tgs$ --> Kerberoast (hashcat -m 13100)
  |
  +-- No prefix — Length gin:
  |     +-- 32 chars hex
  |     |     +-- Windows se? --> NTLM (-m 1000)
  |     |     +-- Web/DB se? --> MD5 (-m 0)
  |     +-- 40 chars hex --> SHA-1 (-m 100)
  |     +-- 64 chars hex --> SHA-256 (-m 1400)
  |     +-- 128 chars hex --> SHA-512 (-m 1700)
  |
  +-- Still confused? --> hashid 'HASH' -m
  |
  +-- Crack karo:
        +-- john --format=FORMAT --wordlist=rockyou.txt hash.txt
        +-- hashcat -m MODE hash.txt rockyou.txt
        +-- john --show hash.txt (ya hashcat --show)
```
