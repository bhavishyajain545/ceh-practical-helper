# 🎯 PORT NUMBER DEKHA — NEXT KYA KARO?

---

| Port | Service | Pehle Kya Karo | Cheatsheet |
|---|---|---|---|
| **21** | FTP | `nmap --script ftp-anon` → anonymous login | #02 |
| **22** | SSH | Default creds → hydra brute force | #03 |
| **23** | Telnet | `telnet <IP>` → default creds | #07 |
| **25** | SMTP | `smtp-user-enum -M VRFY` → users enumerate | #10 |
| **53** | DNS | `dig axfr @<IP> domain` → zone transfer | #09 |
| **80** | HTTP | `gobuster` → `nikto` → SQLi/XSS check | #05 |
| **111** | RPC/NFS | `showmount -e <IP>` → mount shares | #11 |
| **135** | MSRPC | `rpcclient -U "" -N <IP>` | - |
| **139** | NetBIOS | `nbtscan <IP>` → `enum4linux` | #01 |
| **161** | SNMP (UDP) | `snmpwalk -v2c -c public <IP>` | #08 |
| **443** | HTTPS | Same as 80 + SSL cert inspect | #05 |
| **445** | SMB | `enum4linux` → EternalBlue check | #01 |
| **1433** | MSSQL | `mssqlclient.py sa:password@<IP>` | #04 |
| **1521** | Oracle | `odat` tool → TNS listener | - |
| **1524** | Bindshell | `nc <IP> 1524` → instant shell! | - |
| **2049** | NFS | `showmount -e` → mount | #11 |
| **3306** | MySQL | `mysql -h <IP> -u root` (empty pass) | #04 |
| **3389** | RDP | `hydra` brute → `xfreerdp` | #06 |
| **5432** | PostgreSQL | `psql -h <IP> -U postgres` | #04 |
| **5900** | VNC | `hydra -P rockyou.txt <IP> vnc` | #12 |
| **5985** | WinRM | `evil-winrm -i <IP> -u user -p pass` | - |
| **6667** | IRC | UnrealIRCd backdoor (Metasploit) | - |
| **8080** | HTTP Alt | Same as port 80 | #05 |
| **8443** | HTTPS Alt | Same as port 443 | #05 |
| **8180** | Tomcat | Default creds → WAR deploy | #05 |
| **27017** | MongoDB | `mongo <IP>` (no auth?) | - |

---

## First 3 Commands on ANY Target:
```bash
nmap -sV -sC -p- -T4 <IP>                 # 1. Full scan
nmap --script vuln <IP>                    # 2. Vuln scan
searchsploit <service> <version>           # 3. Known exploits
```

---

## INSTANT WINS (try these first):
```
Port 1524 open → nc <IP> 1524 → instant root shell
Port 21 vsftpd 2.3.4 → Metasploit backdoor → instant shell
Port 6667 UnrealIRCd → Metasploit → instant shell
Port 445 Win7/2008 → MS17-010 EternalBlue → SYSTEM shell
Port 5900 no auth → vncviewer <IP> → desktop access
MySQL root empty pass → instant DB access
```
