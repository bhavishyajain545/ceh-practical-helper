# smbclient — SMB share client (FTP-like)

> **The `ftp` client of SMB.** List shares, connect, download/upload files. First tool to confirm anonymous access.

**Install check (Parrot — already installed):** `smbclient --version`

---

## 🎯 Cheat-flow: "What do I run?"

| You need to... | Run this | Why |
|---|---|---|
| **List shares anonymously** | `smbclient -L //<IP> -N` | Null session share list |
| List shares with creds | `smbclient -L //<IP> -U user%pass` | Auth'd list |
| **Connect to a share** | `smbclient //<IP>/share -N` | Interactive shell |
| Connect with creds | `smbclient //<IP>/share -U user%pass` | Auth'd |
| Download a file | `get filename` (inside session) | FTP-style |
| Download everything | `smbclient //<IP>/share -N -c 'prompt OFF;recurse ON;mget *'` | Recursive pull |
| Upload a file | `put filename` | For write-enabled shares |
| Specify domain | `-W DOMAIN -U user%pass` | For AD |
| Pass-the-hash | `--pw-nt-hash -U user%<NThash>` | PtH login |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-L //<IP>` | **List shares** on host |
| `-N` | **No password** (null / anonymous session) |
| `-U user` | Username (prompts for pass) |
| `-U user%pass` | User and password inline |
| `-U 'DOMAIN\user%pass'` | Include domain |
| `-W DOMAIN` | Workgroup / domain |
| `-P` | Connect to printer |
| `-c '<cmd;cmd>'` | Run commands non-interactively |
| `-m SMB2` / `-m SMB3` | Force SMB version |
| `--pw-nt-hash` | Treat password as NT hash (PtH) |
| `-p 445` | Specify port |
| `-I <IP>` | Target IP (when using NetBIOS name) |

### Interactive shell commands
| Cmd | Meaning |
|---|---|
| `ls` / `dir` | List current dir |
| `cd <dir>` | Change dir |
| `get <file>` | Download |
| `put <file>` | Upload |
| `mget *` | Multi-download |
| `recurse ON` | Enable recursion |
| `prompt OFF` | Don't ask per file |
| `mask ""` | Clear mask for mget |
| `!<cmd>` | Run local shell command |
| `exit` / `quit` | Leave |

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Anonymous share enumeration
smbclient -L //<IP> -N

# 2. Anonymous connect to a share
smbclient //<IP>/public -N

# 3. Authenticated list
smbclient -L //<IP> -U 'admin%Password123'

# 4. Grab a single file non-interactively
smbclient //<IP>/share -N -c 'get secret.txt'

# 5. Recursive download (dump the share)
smbclient //<IP>/share -N -c 'prompt OFF;recurse ON;mget *'

# 6. Upload a webshell to a writable share
smbclient //<IP>/wwwroot -U 'user%pass' -c 'put shell.aspx'

# 7. Pass-the-hash
smbclient //<IP>/C$ -U 'Administrator' --pw-nt-hash \
  -U 'Administrator%aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0'

# 8. Force SMB2 (if SMB1 is disabled)
smbclient -L //<IP> -N -m SMB2
```

---

## ⚠️ Gotchas

- **`NT_STATUS_ACCESS_DENIED` on `-L`** → null sessions disabled. Try any cred (`-U 'guest%'`).
- **`NT_STATUS_CONNECTION_DISCONNECTED`** → SMB1 disabled on server. Add `-m SMB2`.
- **Forward slashes** on Linux: `//<IP>/share` — backslashes need quoting.
- **Path inside share** uses backslashes: `cd \Users\Administrator`.
- **Anonymous read ≠ anonymous write.** Use [smbmap](./smbmap.md) to see write perms fast.
- **Hidden shares** (`C$`, `ADMIN$`, `IPC$`) won't show with `-L` — connect directly with creds.
- **Double-quote the `%`** in zsh: `-U 'user%pass'`.

---

## 🔗 Related

- [enum4linux](./enum4linux.md) — bulk SMB enum
- [smbmap](./smbmap.md) — permissions-at-a-glance
- [crackmapexec](./crackmapexec.md) — bulk cred spray
- [impacket](./impacket.md) — `smbserver.py` to host shares yourself
- [nmap](./nmap.md) — `smb-enum-shares` NSE
