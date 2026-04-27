# HIDDEN DIRECTORIES / FILES DHUNDO

---

## Pehle Samjho: Directory Enumeration Kya Hai?

Jab tum kisi website ko browser mein open karte ho, toh sirf wahi pages dikhte hain jo links mein hain.
Lekin server pe bahut saari hidden directories aur files hoti hain — admin panels, backup files, config files, uploaded shells — jo directly linked nahi hoti.

**Directory enumeration** matlab hai in hidden paths ko dhundna by trying thousands of common names automatically.

**Kaise kaam karta hai:**
1. Tool ek wordlist uthata hai (jismein hazaaron common directory/file names hain)
2. Har naam ke liye request bhejta hai: `/admin`, `/backup`, `/config`, etc.
3. Jo paths exist karti hain (200 OK, 301 Redirect) wo report karti hain
4. Jo nahi hoti (404 Not Found) wo skip ho jaati hain

**Kyun zaruri hai?**
- Admin panel mil sakta hai → brute force login
- Backup files → source code / credentials
- .git folder → poora source code
- Config files → database passwords
- Upload directory → file upload exploit ke liye path

---

## CASE 1: Gobuster — Sabse Fast Tool

```bash
# Basic scan — common wordlist (chhoti, fast)
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -t 30

# Thorough scan — badi wordlist
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30

# Extensions bhi check karo (PHP, HTML, TXT files)
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -t 30 -x php,html,txt,bak,old,zip

# Specific status codes filter karo
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -t 30 -x php -s 200,301,302

# Custom port pe
gobuster dir -u http://<IP>:8080 -w /usr/share/wordlists/dirb/common.txt -t 30

# HTTPS pe
gobuster dir -u https://<IP> -w /usr/share/wordlists/dirb/common.txt -t 30 -k
# -k = SSL errors ignore karo
```

**Output samjho:**
```
/admin          (Status: 301) [Size: 312]     → directory hai, redirect ho rahi hai
/login.php      (Status: 200) [Size: 1234]    → page exist karta hai
/backup         (Status: 403) [Size: 287]     → forbidden but EXISTS!
/config.php     (Status: 200) [Size: 0]       → exists but empty (ya PHP executed)
```

**Status codes ka matlab:**
| Code | Matlab | Action |
|---|---|---|
| 200 | OK, page mil gaya | Open karo browser mein |
| 301/302 | Redirect | Follow karo — probably directory hai |
| 403 | Forbidden | Exist karta hai! Access denied but try karo |
| 404 | Not found | Exist nahi karta — skip |
| 500 | Server error | Interesting — investigate karo |

---

## CASE 2: Dirb — Simple Aur Default Wordlist

```bash
# Default wordlist se scan (no arguments needed)
dirb http://<IP>

# Custom wordlist
dirb http://<IP> /usr/share/wordlists/dirb/big.txt

# Extensions add karo
dirb http://<IP> -X .php,.html,.txt

# Specific status codes ignore
dirb http://<IP> -N 403
```

**Gobuster vs Dirb:**
- Gobuster: Fast (multi-threaded), manual configuration
- Dirb: Slow lekin out-of-the-box kaam karta hai, recursive scanning built-in

---

## CASE 3: Ffuf — Sabse Flexible Fuzzer

```bash
# Basic directory fuzzing (FUZZ keyword = jahan wordlist ke words jayenge)
ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirb/common.txt

# 404 responses filter out karo
ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirb/common.txt -fc 404

# Sirf 200 aur 301 dikhao
ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirb/common.txt -mc 200,301

# File extension fuzzing
ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirb/common.txt -e .php,.html,.txt,.bak

# Subdomain fuzzing (bonus!)
ffuf -u http://FUZZ.target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Response size se filter (same size = generic 404)
ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirb/common.txt -fs 1234
```

**Ffuf kyun special hai?** `FUZZ` keyword kahi bhi daal sakte ho — URL, headers, POST data mein. Bahut versatile tool hai.

---

## CASE 4: Nikto — Vulnerability Scanner + Directory Finder

```bash
# Basic scan
nikto -h http://<IP>

# Specific port
nikto -h http://<IP>:8080

# HTTPS
nikto -h https://<IP>

# Output save karo
nikto -h http://<IP> -o nikto_output.txt
```

**Nikto kya alag karta hai:**
- Sirf directories nahi dhundta — **known vulnerabilities** bhi detect karta hai
- Outdated software versions identify karta hai
- Default files aur misconfigurations check karta hai
- Slow hai lekin information-rich hai

**Output mein dekho:**
- "OSVDB-XXXX" entries = known vulnerabilities
- "Allowed HTTP Methods" = PUT/DELETE methods (dangerous!)
- Directory listings = files visible hain

---

## CASE 5: Manual Checks — Pehle Ye Karo

```bash
# robots.txt — websites crawlers ko batati hain kya nahi crawl karna
http://<IP>/robots.txt
# Agar "Disallow: /secret-admin/" likha hai = JACKPOT! Admin nahi chahta ki ye dikhe

# sitemap.xml — site structure
http://<IP>/sitemap.xml

# .git exposure — source code leak!
http://<IP>/.git/
http://<IP>/.git/HEAD
http://<IP>/.git/config
# Agar accessible hai:
git-dumper http://<IP>/.git/ ./git-output    # poora repo download

# .env file — environment variables (passwords!)
http://<IP>/.env

# Common backup files
http://<IP>/backup.zip
http://<IP>/backup.sql
http://<IP>/database.sql
http://<IP>/db.sql
http://<IP>/site.tar.gz
```

---

## Common Interesting Paths — Ye Dhundho

| Path | Kyun Important Hai |
|---|---|
| `/admin/` | Admin panel — login brute force / default creds |
| `/administrator/` | CMS admin (Joomla) |
| `/wp-admin/` | WordPress admin panel |
| `/wp-login.php` | WordPress login |
| `/phpmyadmin/` | Database management — try default creds |
| `/login.php` | Login page → SQLi / brute force |
| `/robots.txt` | Hidden paths revealed |
| `/.git/` | Source code leak — git-dumper se download |
| `/.env` | Environment variables — DB passwords, API keys |
| `/config.php` | Configuration file with credentials |
| `/backup/` | Backup files — source code, database dumps |
| `/uploads/` | Upload directory — file upload attack ke liye |
| `/shell.php` | Kisi aur ne pehle se shell upload ki hai? |
| `/test.php` | Test files with phpinfo() ya debug info |
| `/info.php` | phpinfo() — PHP configuration details |
| `/server-status` | Apache server status page |
| `/console` | Python/Werkzeug debug console |
| `/api/` | API endpoints — data exposure |

---

## Wordlist Selection Guide

| Wordlist | Size | Kab Use Karo |
|---|---|---|
| `/usr/share/wordlists/dirb/common.txt` | ~4600 words | Quick scan, pehle ye try karo |
| `/usr/share/wordlists/dirb/big.txt` | ~20000 words | Common se kuch nahi mila toh |
| `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` | ~220000 words | Thorough scan |
| `/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt` | ~30000 words | Good alternative |
| `/usr/share/seclists/Discovery/Web-Content/common.txt` | ~4700 words | SecLists version |

**Strategy:** Pehle chhoti wordlist se fast scan karo. Kuch interesting nahi mila? Badi wordlist try karo. Extensions bhi add karo (`-x php,html,txt,bak`).

---

## Common Mistakes (Mat Karna Ye)

1. **Extensions add nahi kiye:** Sirf directories dhundoge toh `login.php` ya `config.bak` miss ho jayega — always `-x php,html,txt`
2. **robots.txt manually check nahi kiya:** Tools kabhi kabhi miss kar deti hain — browser mein khud check karo
3. **403 ko ignore kar diya:** Forbidden matlab exist karta hai! Try karo — bypass possible ho sakta hai
4. **Wordlist bahut chhoti:** `common.txt` se start karo lekin agar kuch nahi mila toh `medium.txt` use karo
5. **Port galat hai:** Agar website 8080 pe hai toh URL mein port daalna mat bhoolna
6. **HTTPS pe `-k` nahi lagaya:** Self-signed cert pe SSL error aayega — `-k` flag add karo
7. **.git dhundke download nahi kiya:** `.git/HEAD` accessible hai toh `git-dumper` se poora repo download karo

---

## Quick Decision Tree

```
Web server mila — directories dhundni hain?
  │
  ├─ Pehle manual check karo:
  │    ├─ /robots.txt → hidden paths?
  │    ├─ /.git/HEAD → source code leak?
  │    ├─ /.env → credentials?
  │    └─ /phpmyadmin/, /admin/ → direct try
  │
  ├─ Quick automated scan:
  │    └─ gobuster dir -u URL -w common.txt -t 30 -x php,html,txt
  │
  ├─ Quick scan se kuch nahi mila?
  │    └─ Badi wordlist use karo: directory-list-2.3-medium.txt
  │
  ├─ Vulnerability scan chahiye?
  │    └─ nikto -h URL
  │
  ├─ Kuch mila — ab kya?
  │    ├─ Admin panel → default creds / brute force / SQLi
  │    ├─ Login page → SQLi try karo
  │    ├─ Upload dir → file upload attack
  │    ├─ Backup file → download karo, source code/creds dhundho
  │    ├─ .git → git-dumper se download, source code analyze
  │    └─ phpinfo → PHP version, disabled functions check
  │
  └─ Tool selection:
       ├─ Fast scan chahiye → Gobuster
       ├─ Simple/default → Dirb
       ├─ Flexible/custom → Ffuf
       └─ Vuln scan → Nikto
```
